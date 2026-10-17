[Previous](./[9]-Routers-And-Project-Structure.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[11]-WebSockets.md)

*Core Features*

# Lesson 10 - Middleware And Background Tasks

## 10.1 Writing middleware

**Middleware** is code that wraps every request. It runs **before** the request reaches a path operation and **after** the response is created.

```
Request  →  middleware  →  path operation  →  middleware  →  Response
```

Typical uses: timing, logging, adding headers, request IDs, compression, and security rules.

The simplest way to write one is the `@app.middleware("http")` decorator:

```python
import time

from fastapi import FastAPI, Request

app = FastAPI()


@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    start = time.perf_counter()

    response = await call_next(request)     # pass the request on

    duration = time.perf_counter() - start
    response.headers["X-Process-Time"] = f"{duration:.4f}"
    return response
```

How it works:

1. The function receives the `request` and `call_next`.
2. Code **before** `await call_next(request)` runs on the way in.
3. `call_next` runs the rest of the app and returns the `response`.
4. Code **after** it runs on the way out, where you can modify the response.

A logging example with a request ID:

```python
import logging
import uuid

logger = logging.getLogger("app")


@app.middleware("http")
async def log_requests(request: Request, call_next):
    request_id = uuid.uuid4().hex[:8]
    logger.info("[%s] %s %s", request_id, request.method, request.url.path)

    response = await call_next(request)

    response.headers["X-Request-ID"] = request_id
    logger.info("[%s] -> %s", request_id, response.status_code)
    return response
```

**Order of middleware.** Each middleware you add wraps the previous ones, so the **last added runs first** on the way in.

Built-in middleware you can add with `app.add_middleware(...)`:

| Middleware | Purpose |
|---|---|
| `CORSMiddleware` | Cross-Origin Resource Sharing rules |
| `GZipMiddleware` | Compresses large responses |
| `TrustedHostMiddleware` | Rejects requests with unexpected `Host` headers |
| `HTTPSRedirectMiddleware` | Redirects HTTP to HTTPS |

```python
from fastapi.middleware.gzip import GZipMiddleware

app.add_middleware(GZipMiddleware, minimum_size=1000)
```

Middleware vs dependencies: middleware applies to **every** request (including unknown routes and docs) and cannot easily read parsed path/body parameters. Dependencies apply to selected endpoints and have access to declared parameters.

---

## 10.2 CORS

**CORS** (Cross-Origin Resource Sharing) is a browser rule. A web page from `https://myapp.com` is normally blocked from calling an API on `https://api.myapp.com` unless the API says it is allowed. The browser enforces this; tools such as `curl` and server-to-server calls are not affected.

Add `CORSMiddleware` to tell browsers which origins may call your API:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [
    "http://localhost:3000",
    "https://myapp.com",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Authorization", "Content-Type"],
)
```

Main parameters:

| Parameter | Meaning |
|---|---|
| `allow_origins` | List of allowed origins (scheme + domain + port) |
| `allow_origin_regex` | Regex alternative for patterns such as subdomains |
| `allow_methods` | HTTP methods allowed (`["*"]` means all) |
| `allow_headers` | Request headers allowed (`["*"]` means all) |
| `allow_credentials` | Allow cookies/authorization headers (default `False`) |
| `max_age` | Seconds browsers may cache the preflight answer |

Important details:

- An **origin** is `scheme://host:port`. `http://localhost:3000` and `http://localhost:8000` are different origins.
- For non-simple requests the browser first sends an `OPTIONS` **preflight** request. The middleware answers it automatically.
- `allow_origins=["*"]` allows any origin but **cannot be combined with credentials**; when credentials are needed, list origins explicitly.

A deeper look at CORS security is in Lesson 15.

---

## 10.3 Background tasks

Sometimes work should happen **after** the response has been sent, such as sending an email or writing an audit log. The client should not wait for it. Use `BackgroundTasks`.

```python
from fastapi import BackgroundTasks, FastAPI

app = FastAPI()


def send_welcome_email(email: str, name: str):
    # imagine a slow email-sending call here
    with open("emails.log", "a") as log:
        log.write(f"Sent welcome email to {name} <{email}>\n")


@app.post("/signup", status_code=202)
def signup(email: str, name: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(send_welcome_email, email, name)
    return {"message": "Signed up, check your inbox soon"}
```

How it works:

1. FastAPI gives you a `BackgroundTasks` object when you declare the parameter.
2. `add_task(function, *args, **kwargs)` schedules the function.
3. The response is sent first; then the tasks run.

Notes:

- Tasks can be `def` (run in a thread pool) or `async def` (run on the event loop).
- `BackgroundTasks` also works inside **dependencies**, and FastAPI merges all tasks into one.
- Tasks run in the **same process** as your API. If the server restarts, pending tasks are lost, and heavy tasks will slow down the app.

When to choose something else:

| Need | Use |
|---|---|
| Small, quick work (email, log, cache refresh) | `BackgroundTasks` |
| Long-running, retried, scheduled, or distributed jobs | A task queue such as Celery, ARQ, or RQ (see Lesson 19) |

---

## 10.4 Lifespan events

A **lifespan** function runs code once when the application **starts** and once when it **shuts down**. Use it to create connection pools, load ML models, or open and close shared resources.

```python
from contextlib import asynccontextmanager

from fastapi import FastAPI

resources = {}


@asynccontextmanager
async def lifespan(app: FastAPI):
    # --- Startup ---
    print("Starting up: loading resources")
    resources["settings"] = {"mode": "production"}

    yield   # the application runs while we are paused here

    # --- Shutdown ---
    print("Shutting down: releasing resources")
    resources.clear()


app = FastAPI(lifespan=lifespan)


@app.get("/mode")
def get_mode():
    return resources["settings"]
```

- Everything **before** `yield` is startup logic.
- Everything **after** `yield` is shutdown logic.
- Pass the function to `FastAPI(lifespan=...)`.

Sharing objects through `app.state` is a clean alternative to module-level globals:

```python
from fastapi import Request


@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.client = {"name": "shared-client"}
    yield
    app.state.client = None


app = FastAPI(lifespan=lifespan)


@app.get("/client")
def read_client(request: Request):
    return request.app.state.client
```

The older `@app.on_event("startup")` and `@app.on_event("shutdown")` decorators still work but are **deprecated**. Use `lifespan` in new code. Do not mix both styles in one app, because when `lifespan` is set, the old event handlers are ignored.

Lifespan only runs for the main application. In tests, use `with TestClient(app) as client:` to trigger it (see Lesson 16).

---

[Previous](./[9]-Routers-And-Project-Structure.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[11]-WebSockets.md)
