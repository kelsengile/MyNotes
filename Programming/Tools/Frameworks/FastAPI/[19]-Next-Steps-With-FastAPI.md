[Previous](./[18]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md)

*Quality And Delivery*

# Lesson 19 - Next Steps With FastAPI

You now know how to build, secure, test, document, and deploy a FastAPI application. This final lesson covers how to make it faster, how to move slow work out of the request cycle, how to serve machine learning models, and what to build next.

---

## 19.1 Performance tuning

FastAPI is already fast, so most performance problems come from how the application is written, not from the framework. **Measure first, then optimize.**

### Measure before changing anything

- **Load testing** shows how many requests per second your API handles and how slow the slowest requests are. Tools include `locust`, `k6`, and `wrk`.
- **Profiling** shows which functions use the time. Python's built-in `cProfile` or the `pyinstrument` package both work.
- Look at the **p95 / p99 latency** (the slowest 5% or 1% of requests), not just the average.

### Common wins

| Problem | Fix |
|---|---|
| Blocking code inside `async def` | Use `def`, or switch to an async library (Lesson 2) |
| Many database queries per request (the "N+1 problem") | Load related rows in one query using joins or eager loading (Lesson 12) |
| Opening a new database connection every request | Use connection pooling (Lesson 13) |
| Returning huge lists | Add pagination with `skip` / `limit` (Lesson 3) |
| Repeating the same expensive work | Cache the result |
| Large JSON responses | Enable compression |
| One worker process on a multi-core machine | Run several workers (Lesson 18) |

### Serialization

When you declare a return type or `response_model`, FastAPI serializes the data with Pydantic's fast core. You usually no longer need special response classes such as `ORJSONResponse` just for speed. Prefer declaring a response model:

```python
@app.get("/items", response_model=list[ItemPublic])
def list_items():
    ...
```

### Compression

```python
from fastapi import FastAPI
from fastapi.middleware.gzip import GZipMiddleware

app = FastAPI()
app.add_middleware(GZipMiddleware, minimum_size=1000)  # compress responses over 1000 bytes
```

### Simple in-process caching

For data that rarely changes, an in-memory cache avoids repeated work:

```python
from functools import lru_cache

from fastapi import FastAPI

app = FastAPI()


@lru_cache(maxsize=128)
def load_country_list() -> tuple[str, ...]:
    # Imagine an expensive file read or database call here
    return ("France", "Japan", "Peru")


@app.get("/countries")
def countries():
    return load_country_list()
```

`lru_cache` is per process. With several workers, each has its own copy. For a cache shared across workers and servers, use **Redis** (for example through the `redis` package or `fastapi-cache2`).

### Reuse expensive objects

Create heavy objects (HTTP clients, database engines, ML models) **once** at startup using a lifespan function (Lesson 10), not inside each request.

```python
from contextlib import asynccontextmanager

import httpx
from fastapi import FastAPI, Request


@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.http = httpx.AsyncClient(timeout=10)
    yield
    await app.state.http.aclose()


app = FastAPI(lifespan=lifespan)


@app.get("/proxy")
async def proxy(request: Request):
    response = await request.app.state.http.get("https://example.com")
    return {"status": response.status_code}
```

### Performance checklist

1. Is any `async def` calling blocking code?
2. Are database queries indexed and free of N+1 patterns?
3. Is a connection pool configured?
4. Are list endpoints paginated?
5. Are expensive objects created once at startup?
6. Are multiple workers running in production?

---

## 19.2 Task queues

Lesson 10 introduced `BackgroundTasks`, which runs a function after the response is sent. It is simple, but it has limits:

- The task runs **inside the same process** as your API.
- If the server restarts or crashes, the task is **lost**.
- There is no **retry**, **scheduling**, or **progress tracking**.
- Heavy tasks compete with your API for CPU.

For anything long-running, important, or CPU-heavy, use a **task queue**.

### How a task queue works

```
Client --> FastAPI --(put job on queue)--> Broker (Redis / RabbitMQ)
                                              |
                                              v
                                       Worker process(es)
                                              |
                                              v
                                       Result backend
```

1. The API receives a request and puts a **job** on a **broker**.
2. It immediately responds, usually with `202 Accepted` and a task ID.
3. Separate **worker** processes pick up jobs and run them.
4. The client later asks for the result using the task ID.

### Choosing a tool

| Tool | Notes |
|---|---|
| **Celery** | The most widely used and full-featured. Retries, scheduling, many brokers. Synchronous workers |
| **ARQ** | Small and async-native, built on Redis |
| **RQ** | Simple Redis queue for plain functions |
| **Dramatiq** | Reliable and simpler than Celery |
| **Taskiq** | Async-native with FastAPI-style dependency injection |

### Example with Celery and Redis

Install the packages and run Redis (for example `docker run -p 6379:6379 redis`):

```bash
pip install celery redis
```

`celery_app.py`:

```python
import time

from celery import Celery

celery_app = Celery(
    "worker",
    broker="redis://localhost:6379/0",
    backend="redis://localhost:6379/1",
)


@celery_app.task
def generate_report(user_id: int) -> str:
    time.sleep(10)  # pretend this is slow work
    return f"report-for-user-{user_id}.pdf"
```

`main.py`:

```python
from celery.result import AsyncResult
from fastapi import FastAPI, status

from celery_app import celery_app, generate_report

app = FastAPI()


@app.post("/reports/{user_id}", status_code=status.HTTP_202_ACCEPTED)
def start_report(user_id: int):
    task = generate_report.delay(user_id)
    return {"task_id": task.id}


@app.get("/tasks/{task_id}")
def task_status(task_id: str):
    result = AsyncResult(task_id, app=celery_app)
    return {
        "status": result.status,  # PENDING, STARTED, SUCCESS, FAILURE ...
        "result": result.result if result.successful() else None,
    }
```

Run the API and the worker in separate terminals:

```bash
fastapi dev main.py
celery -A celery_app worker --loglevel=info
```

### Good practices

- Make tasks **idempotent** (safe to run twice), because retries can happen.
- Pass **small, simple arguments** such as IDs, not large objects. Let the worker load data itself.
- Set **time limits** and **retries** on tasks.
- Run workers as a **separate service** from the API (for example, a second container).

---

## 19.3 Serving ML models

FastAPI is a very popular way to expose a trained model as an HTTP API. The same ideas apply to scikit-learn, PyTorch, TensorFlow, or large language model clients.

### Core rules

1. **Load the model once at startup**, never per request. Loading is slow.
2. **Validate inputs** with Pydantic so bad data never reaches the model.
3. **Use plain `def`** for CPU-bound predictions so FastAPI runs them in a thread pool, or move heavy inference to a worker or dedicated service.
4. **Return a clear, typed response.**

### Step 1: train and save a small model

`train.py`:

```python
import joblib
from sklearn.datasets import load_iris
from sklearn.linear_model import LogisticRegression

iris = load_iris()
model = LogisticRegression(max_iter=500).fit(iris.data, iris.target)

joblib.dump({"model": model, "names": list(iris.target_names)}, "iris_model.joblib")
```

```bash
pip install scikit-learn joblib
python train.py
```

### Step 2: serve it

`main.py`:

```python
from contextlib import asynccontextmanager

import joblib
from fastapi import FastAPI
from pydantic import BaseModel, Field

artifacts: dict = {}


@asynccontextmanager
async def lifespan(app: FastAPI):
    artifacts.update(joblib.load("iris_model.joblib"))  # load once
    yield
    artifacts.clear()


app = FastAPI(title="Iris Classifier", lifespan=lifespan)


class IrisFeatures(BaseModel):
    sepal_length: float = Field(gt=0, examples=[5.1])
    sepal_width: float = Field(gt=0, examples=[3.5])
    petal_length: float = Field(gt=0, examples=[1.4])
    petal_width: float = Field(gt=0, examples=[0.2])


class Prediction(BaseModel):
    species: str
    confidence: float


@app.post("/predict", response_model=Prediction)
def predict(features: IrisFeatures):
    row = [[
        features.sepal_length,
        features.sepal_width,
        features.petal_length,
        features.petal_width,
    ]]
    model = artifacts["model"]
    probabilities = model.predict_proba(row)[0]
    best = int(probabilities.argmax())
    return Prediction(
        species=artifacts["names"][best],
        confidence=float(probabilities[best]),
    )


@app.get("/health")
def health():
    return {"status": "ok", "model_loaded": "model" in artifacts}
```

Try it in `/docs` with the example values. The response should be `setosa` with high confidence.

### Production tips for ML serving

- **Health endpoint**: lets Docker or Kubernetes know the model finished loading.
- **Version your models**: include a model version in the response or URL (such as `/v1/predict`).
- **Batch requests**: accept a list of inputs when possible. Models are much faster on batches.
- **Heavy models (deep learning, LLMs)**: use a GPU server or a dedicated inference service, with FastAPI as the front door. Offload long jobs to a task queue (see 19.2).
- **Stream outputs**: for text generation, return a `StreamingResponse` (Lesson 8) so users see tokens as they are produced.
- **Memory with many workers**: each worker process loads its own copy of the model, so count that in your memory budget.

---

## 19.4 Project ideas

The best way to learn is to build. Each idea below combines several lessons. Start with the first and move down as you gain confidence.

| Level | Project | Lessons it practices |
|---|---|---|
| Beginner | **To-do API**: create, list, update, delete tasks | 3, 4, 5, 7 |
| Beginner | **Notes API with search and tags**: query filters and pagination | 3, 4, 5 |
| Intermediate | **Blog backend**: users, posts, comments in a SQL database, with login and ownership rules | 6, 9, 12, 13, 14 |
| Intermediate | **URL shortener**: unique short codes, redirects, click counting | 5, 7, 12, 15 |
| Intermediate | **File sharing service**: uploads, size limits, expiring download links | 8, 10, 14, 15 |
| Intermediate | **Chat room**: real-time messages between many users | 11, 14 |
| Advanced | **Image or document processing service**: upload a file, process it in a worker, poll for the result | 8, 10, 19.2 |
| Advanced | **ML prediction service**: serve a trained model with a health check and a Docker image | 16, 18, 19.3 |
| Advanced | **Multi-tenant SaaS API**: organizations, roles and scopes, rate limiting, migrations, CI tests | 6, 12, 13, 14, 15, 16, 18 |

### How to approach a project

1. **Write down the endpoints first** (method, path, request, response).
2. **Create Pydantic models** for requests and responses.
3. **Build with an in-memory list** first, then replace it with a database.
4. **Add authentication** once the main features work.
5. **Write tests** for each endpoint as you go (Lesson 16).
6. **Document** it with good summaries, tags, and examples (Lesson 17).
7. **Containerize and deploy** it (Lesson 18).

### Where to keep learning

- The official FastAPI documentation at `fastapi.tiangolo.com`, including its advanced user guide.
- The Pydantic documentation for advanced validation and settings management.
- The Starlette documentation for the underlying toolkit.
- SQLAlchemy and Alembic documentation for deeper database work.
- Reading the source code of open-source FastAPI projects on GitHub to see how real applications are structured.

Congratulations on completing this Topic. Keep building, keep testing, and keep reading other people's code.

---

[Previous](./[18]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md)