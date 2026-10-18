[Previous](./[17]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Flask.md)

*Quality And Delivery*

# Lesson 18 - Next Steps With Flask

---

## 18.1 Async In Flask

### Sync vs async

Flask is a **WSGI** (synchronous) framework: each worker handles one request at a time. That works well for most apps. But when a view spends most of its time **waiting** (calling external APIs, for example), asynchronous code can make better use of that waiting time.

### Async views

Flask supports `async def` views. Install the optional async extra:

```bash
pip install "flask[async]"
```

```python
import asyncio
import httpx
from flask import Flask

app = Flask(__name__)


@app.route("/dashboard")
async def dashboard():
    async with httpx.AsyncClient(timeout=5) as client:
        weather, news = await asyncio.gather(
            client.get("https://api.example.com/weather"),
            client.get("https://api.example.com/news"),
        )
    return {"weather": weather.json(), "news": news.json()}
```

Here two network calls run **concurrently**, so the total time is roughly the slowest call rather than the sum of both.

### What actually happens

Flask runs each async view in its **own event loop**, inside the worker's thread for that request. So:

| Fact | Consequence |
|---|---|
| The request is still handled by a WSGI worker. | A worker is occupied until the view finishes. |
| Concurrency exists **within** a single request. | Great for fan-out calls to several services. |
| It does not make Flask handle thousands of idle connections. | It is not a replacement for an ASGI framework. |

Async hooks also work (`before_request`, `after_request`, and error handlers may be `async def`).

### Limitations

- **Long-lived connections** (WebSockets, server-sent events with many clients) are poorly suited to WSGI workers. Use a dedicated approach such as Flask-SocketIO with an appropriate server, or an ASGI framework.
- **Libraries**: Flask-SQLAlchemy's standard session is synchronous. Do not mix blocking database calls into async code expecting concurrency benefits.
- **Context**: `request`, `current_app`, and `g` work in async views, but be careful when creating background tasks with `asyncio.create_task`, since they may outlive the request context.

### Running Flask on an ASGI server

If you need an ASGI server, you can wrap the app with `asgiref`:

```python
from asgiref.wsgi import WsgiToAsgi
from myapp import create_app

asgi_app = WsgiToAsgi(create_app())
```

```bash
pip install uvicorn asgiref
uvicorn wsgi_asgi:asgi_app
```

This lets an ASGI server host Flask, but the app code still runs synchronously.

### Quart: Flask-style, natively async

**Quart** is a framework with a Flask-compatible API built on ASGI. If your project is async-first (heavy WebSockets, streaming), Quart lets you keep familiar Flask patterns.

### When to choose async

| Use async when... | Stay synchronous when... |
|---|---|
| A view makes several independent external calls. | The app mostly reads and writes a database. |
| You need to aggregate multiple APIs. | Traffic is modest. |
| You already use async libraries. | You rely on sync-only extensions. |

For slow work that does not need to finish before responding, use **background tasks** (next section).

---

## 18.2 Celery Background Tasks

Some work is too slow to do inside a request: sending email, generating reports, resizing images, calling slow APIs. Making users wait leads to timeouts and poor experience. A **task queue** moves the work to separate **worker** processes.

### How Celery works

```
Flask app  →  Broker (Redis / RabbitMQ)  →  Celery worker(s)
   |                                              |
   └────────────── Result backend (optional) ←────┘
```

1. Your view **enqueues** a task (a message describing what to do).
2. A **broker** stores the message.
3. A **Celery worker** picks it up and runs it independently of the web process.
4. Optionally, a **result backend** stores the outcome so you can check status later.

### Installation

```bash
pip install "celery[redis]"
```

You also need a running Redis server (for example with `docker run -p 6379:6379 redis`).

### Integrating Celery with Flask

`myapp/__init__.py`:

```python
from celery import Celery, Task
from flask import Flask


def celery_init_app(app: Flask) -> Celery:
    class FlaskTask(Task):
        def __call__(self, *args, **kwargs):
            with app.app_context():
                return self.run(*args, **kwargs)

    celery_app = Celery(app.name, task_cls=FlaskTask)
    celery_app.config_from_object(app.config["CELERY"])
    celery_app.set_default()
    app.extensions["celery"] = celery_app
    return celery_app


def create_app() -> Flask:
    app = Flask(__name__)
    app.config.from_mapping(
        CELERY=dict(
            broker_url="redis://localhost:6379/0",
            result_backend="redis://localhost:6379/0",
            task_ignore_result=True,
        ),
    )
    celery_init_app(app)

    from .views import bp
    app.register_blueprint(bp)
    return app
```

The custom `FlaskTask` wraps every task in an **application context**, so tasks can use `current_app`, `db`, and other Flask features.

### Writing tasks

`myapp/tasks.py`:

```python
import time
from celery import shared_task


@shared_task(ignore_result=False)
def add(a, b):
    return a + b


@shared_task
def send_welcome_email(user_id):
    time.sleep(3)    # stand-in for slow work
    print(f"Email sent to user {user_id}")
```

`@shared_task` creates tasks that are not tied to a specific Celery app object, which works well with factories.

### Calling tasks from views

`myapp/views.py`:

```python
from celery.result import AsyncResult
from flask import Blueprint, request

from . import tasks

bp = Blueprint("tasks", __name__)


@bp.post("/signup")
def signup():
    user_id = 42    # create the user here
    tasks.send_welcome_email.delay(user_id)       # returns immediately
    return {"status": "signed up"}, 202


@bp.post("/add")
def start_add():
    result = tasks.add.delay(request.json["a"], request.json["b"])
    return {"task_id": result.id}, 202


@bp.get("/result/<task_id>")
def get_result(task_id):
    result = AsyncResult(task_id)
    return {
        "ready": result.ready(),
        "successful": result.successful() if result.ready() else None,
        "value": result.get() if result.ready() and result.successful() else None,
    }
```

- `.delay(...)` is a shortcut for `.apply_async(args=(...))`.
- Pass **simple, serializable arguments** (IDs, strings, numbers), not model objects. Look the objects up inside the task.
- `202 Accepted` signals that work has been accepted but is not finished.

### Starting the worker

Create `make_celery.py` in the project root:

```python
from myapp import create_app

flask_app = create_app()
celery_app = flask_app.extensions["celery"]
```

Then run:

```bash
celery -A make_celery worker --loglevel INFO
```

### Scheduled and retried tasks

```python
@shared_task(bind=True, autoretry_for=(ConnectionError,), retry_backoff=True, max_retries=5)
def sync_with_partner(self, record_id):
    ...
```

For periodic jobs (nightly cleanups, hourly reports), use **Celery Beat**:

```bash
celery -A make_celery beat --loglevel INFO
```

### Good practices

- Make tasks **idempotent**, so running one twice does no harm (tasks may retry).
- Keep tasks small and focused.
- Pass IDs, not objects.
- Monitor workers (for example with **Flower**).
- For lightweight needs, simpler tools such as **RQ** or **Huey** are alternatives.

---

## 18.3 Moving To FastAPI Or Django

Flask is excellent, but it is not the only choice. Understanding the alternatives helps you pick the right tool and transfer what you learned.

### Comparison

| Feature | Flask | FastAPI | Django |
|---|---|---|---|
| Type | Micro-framework (WSGI) | API framework (ASGI) | Full-stack framework (WSGI/ASGI) |
| Philosophy | Minimal; pick your parts | Fast APIs with type hints | Batteries included |
| Async | Limited | Native | Supported |
| Data validation | Add Marshmallow/Pydantic | Built-in (Pydantic) | Forms and serializers (DRF) |
| Auto API docs | Add extensions | Built-in (OpenAPI/Swagger) | Via Django REST Framework tools |
| ORM | Add SQLAlchemy | Add SQLAlchemy/others | Built-in |
| Admin interface | Flask-Admin | Third party | Built-in |
| Authentication | Extensions | Third party / libraries | Built-in |
| Templates | Jinja2 | Jinja2 (optional) | Django templates |
| Learning curve | Gentle | Moderate | Steeper at first |
| Best for | Small to mid apps, prototypes, microservices | High-performance APIs, async workloads | Large sites, content apps, rapid full-stack development |

### FastAPI at a glance

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Task(BaseModel):
    title: str
    done: bool = False


@app.post("/tasks", status_code=201)
async def create_task(task: Task):
    return task
```

Run with `uvicorn main:app --reload` and open `/docs` for interactive documentation generated automatically. The type hints drive validation and docs, so much of what you did manually with Marshmallow (Lesson 12) is built in.

### Django at a glance

Django ships with an ORM, migrations, admin panel, authentication, forms, and a strict project structure. Where Flask asks "which library do you want?", Django already answered. A model and admin registration immediately give you a working management interface:

```python
# models.py
from django.db import models

class Task(models.Model):
    title = models.CharField(max_length=100)
    done = models.BooleanField(default=False)
```

### Choosing

| If you want... | Consider |
|---|---|
| Full control and a small footprint | **Flask** |
| A modern JSON API with automatic docs and async | **FastAPI** |
| A complete site with admin and auth out of the box | **Django** |
| Flask-style code with native async | **Quart** |

### Skills that transfer

Everything you learned here applies elsewhere:

- HTTP methods, status codes, and REST design.
- Routing, request/response cycles, and middleware ideas.
- Templates, forms, sessions, and CSRF.
- ORMs, migrations, and transactions (SQLAlchemy is used with FastAPI too).
- Testing, security practices, and deployment (Gunicorn/Uvicorn, Nginx, Docker).

### Do you need to switch?

Not necessarily. Flask comfortably powers production systems. Consider switching only when a specific need appears: heavy async workloads, an API-centric product that benefits from automatic validation and docs, or a large content-driven site that would take significant effort to assemble from parts.

---

## 18.4 Project Ideas

Building real projects is the best way to cement what you learned. These ideas progress from simple to ambitious. Each lists the lessons that apply.

### Beginner

| Project | What you practice |
|---|---|
| **Personal portfolio site** | Routing, templates, inheritance, static files (Lessons 2, 4). |
| **Contact form with email** | Flask-WTF, validation, flash messages, Flask-Mail (Lessons 5, 16). |
| **Unit converter** | Routes with variables, forms, query strings (Lessons 2, 3). |
| **Quote of the day** | Rendering data, JSON files, simple APIs (Lessons 3, 12). |

### Intermediate

| Project | What you practice |
|---|---|
| **To-do list app with accounts** | SQLAlchemy models, Flask-Login, ownership checks, CRUD (Lessons 10, 13). |
| **Personal blog** | Blueprints, pagination, Markdown rendering, image uploads, admin area (Lessons 6, 10, 14). |
| **URL shortener** | Unique constraints, redirects, click counting, rate limiting (Lessons 3, 10, 14). |
| **Expense tracker** | Relationships, aggregation queries, charts with a JavaScript library (Lesson 10). |
| **REST API for a library** | Marshmallow schemas, status codes, API versioning, tests (Lessons 12, 15). |

### Advanced

| Project | What you practice |
|---|---|
| **Link-in-bio / mini social network** | Auth, many-to-many relationships (followers), feeds, pagination (Lessons 10, 13). |
| **Job board with search** | Filtering, full-text search, email alerts via Celery (Lessons 10, 18). |
| **Image gallery with background processing** | Uploads, thumbnails generated by Celery tasks, object storage (Lessons 14, 18). |
| **Real-time chat** | Flask-SocketIO, rooms, authentication of sockets. |
| **Booking system** | Transactions, conflict handling, scheduled reminders, calendar views. |
| **Multi-tenant SaaS starter** | Application factory, config per environment, migrations, Stripe-style payments, Docker deployment (Lessons 6, 7, 11, 17). |

### How to approach a project

1. **Define the smallest useful version** (a minimum viable product).
2. **Sketch the pages and data**: list routes and database tables on paper.
3. **Set up the structure**: virtual environment, application factory, configuration, Git repository.
4. **Build one feature at a time**, committing often.
5. **Write tests** for important behavior as you go.
6. **Secure it**: validate input, hash passwords, set cookie flags, review the checklist in Lesson 14.
7. **Deploy early**, even a rough version, to learn the process.
8. **Iterate** based on feedback.

### Keep growing

- Read the official **Flask documentation** and its tutorial (the Flaskr blog example).
- Read the source code of well-made open-source Flask projects.
- Learn **SQL** well; it makes you better with any ORM.
- Study **HTTP** and web security basics (the OWASP resources).
- Explore front-end tooling (JavaScript frameworks, HTMX) to complement your Flask back end.
- Contribute to open source: fix a documentation typo, then a small bug.

You now have a complete foundation in Flask, from routing and templates through databases, authentication, testing, and deployment. Pick a project from the lists above, start small, and keep building.

---

[Previous](./[17]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Flask.md)
