[Previous](./[17]-OpenAPI-And-Documentation.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[19]-Next-Steps-With-FastAPI.md)

*Quality And Delivery*

# Lesson 18 - Deployment

## 18.1 Uvicorn and Gunicorn

**Deploying** means running your app on a server where real users can reach it. In development you use `fastapi dev` (with auto-reload). In production you need a stable server setup.

**Running the app in production mode:**

```bash
fastapi run app/main.py
```

This starts Uvicorn without auto-reload, listening on `0.0.0.0` (all network interfaces) on port `8000` by default. You can also call Uvicorn directly:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

**Multiple workers.** One Uvicorn process uses only one CPU core. To use more cores, run several **worker processes**:

```bash
fastapi run app/main.py --workers 4
# or
uvicorn app.main:app --workers 4
```

A common rule of thumb is one worker per CPU core, then adjust after measuring.

**Gunicorn** is a mature process manager. It can supervise Uvicorn workers, restarting them if they crash:

```bash
pip install gunicorn uvicorn-worker
```

```bash
gunicorn app.main:app \
  --workers 4 \
  --worker-class uvicorn_worker.UvicornWorker \
  --bind 0.0.0.0:8000
```

(`uvicorn_worker` is the maintained package for this; the older `uvicorn.workers.UvicornWorker` is deprecated.)

Which to choose?

| Setup | When it fits |
|---|---|
| `fastapi run` / `uvicorn --workers N` on a single machine | Simple deployments, small to medium traffic |
| Gunicorn + Uvicorn workers | Traditional servers where you want a battle-tested process manager |
| **One process per container**, scaled by an orchestrator (Kubernetes, ECS, Cloud Run) | Containerized setups. The platform handles replication, so you often run **a single worker per container** |

**A reverse proxy** such as Nginx, Caddy, or Traefik normally sits in front of your app. It provides **HTTPS/TLS**, serves static files, applies timeouts and body-size limits, and balances load. Tell the app to trust it:

```bash
fastapi run app/main.py --proxy-headers
```

Run the app as a managed service so it restarts automatically (for example `systemd`, Docker with `restart: always`, or a platform that does this for you). Do not leave it running in a terminal session.

---

## 18.2 Docker

**Docker** packages your app and its dependencies into an **image** that runs identically everywhere.

Project layout:

```
.
├── Dockerfile
├── requirements.txt
└── app/
    ├── __init__.py
    └── main.py
```

`requirements.txt`:

```
fastapi[standard]>=0.115
```

`Dockerfile`:

```dockerfile
FROM python:3.12-slim

WORKDIR /code

# Copy requirements first so Docker can cache the install step
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY ./app ./app

# Run as a non-root user for safety
RUN useradd --create-home appuser
USER appuser

EXPOSE 8000

CMD ["fastapi", "run", "app/main.py", "--port", "8000", "--proxy-headers"]
```

Explanation:

1. `FROM` picks a base image. `-slim` variants are smaller.
2. Copying `requirements.txt` **before** the code means dependencies are only reinstalled when that file changes, which makes rebuilds fast.
3. `CMD` uses the **exec form** (a JSON list), which makes the server receive shutdown signals properly. Avoid the shell form (`CMD fastapi run ...`) for this reason.
4. Running as a non-root user limits damage if the container is compromised.

Add a `.dockerignore` file to keep images small and secrets out:

```
.git
.venv
__pycache__
*.pyc
.env
tests
```

Build and run:

```bash
docker build -t shop-api .
docker run -d --name shop-api -p 8000:8000 --env-file .env shop-api
```

Visit `http://localhost:8000/docs`.

`docker-compose.yml` for the app plus a database:

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    env_file: .env
    depends_on:
      - db
    restart: unless-stopped

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: shop
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: shop
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

Run it with `docker compose up --build`.

Tips:

- With Kubernetes or similar, replication is handled outside the container, so prefer **one process per container** and scale the number of containers.
- Use a **health check endpoint** (for example `GET /health` returning `{"status": "ok"}`) so the platform knows when your app is ready.
- Pin versions in `requirements.txt` for repeatable builds.

---

## 18.3 Environment configuration

The same code must run in development, testing, and production with **different settings**: database URL, secret keys, allowed origins. Never hard-code these. Read them from **environment variables** (the "12-factor app" approach).

Use **pydantic-settings** to load, validate, and type configuration:

```bash
pip install pydantic-settings
```

`app/config.py`:

```python
from functools import lru_cache

from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    app_name: str = "Shop API"
    environment: str = "development"
    debug: bool = False
    database_url: str
    secret_key: str
    access_token_expire_minutes: int = 30
    cors_origins: list[str] = ["http://localhost:3000"]

    model_config = SettingsConfigDict(env_file=".env")


@lru_cache
def get_settings() -> Settings:
    return Settings()
```

`.env` (never commit this file to Git; add it to `.gitignore`):

```
DATABASE_URL=postgresql+psycopg://shop:password@localhost/shop
SECRET_KEY=change-me-to-a-long-random-value
ENVIRONMENT=development
CORS_ORIGINS=["http://localhost:3000","https://app.example.com"]
```

Using the settings as a **dependency** (easy to override in tests):

```python
from typing import Annotated

from fastapi import Depends, FastAPI

from app.config import Settings, get_settings

app = FastAPI()


@app.get("/info")
def info(settings: Annotated[Settings, Depends(get_settings)]):
    return {"app_name": settings.app_name, "environment": settings.environment}
```

How it works:

- Field names map to **case-insensitive** environment variable names (`database_url` ↔ `DATABASE_URL`).
- Real environment variables take priority over values in `.env`.
- Missing required values (like `database_url`) make the app **fail at startup** with a clear error, which is much better than failing later.
- `@lru_cache` makes sure the settings are created only once.
- In tests: `app.dependency_overrides[get_settings] = lambda: Settings(database_url="sqlite://", secret_key="test")`.

Secrets in production should come from your platform's secret manager (Kubernetes Secrets, AWS Secrets Manager, Docker secrets, etc.) rather than from a file in the repository.

---

## 18.4 Cloud hosting

Many options exist. Choose based on how much you want to manage yourself.

| Option | Description | Good for |
|---|---|---|
| **FastAPI Cloud** | Hosting built by the FastAPI team, deploys with a CLI command | The quickest path for FastAPI projects |
| **PaaS** (Render, Railway, Fly.io, Heroku) | Connect a repo or Docker image; they build and run it | Small to medium apps, fast setup |
| **Serverless containers** (Google Cloud Run, AWS App Runner, Azure Container Apps) | Run your Docker image, scale automatically, even to zero | Variable traffic, minimal operations |
| **Container orchestration** (Kubernetes, AWS ECS/EKS, GKE, AKS) | Full control and scaling | Large systems, many services |
| **VPS / virtual machine** (DigitalOcean, Hetzner, EC2) | You manage the server: Docker or systemd + Nginx | Full control, predictable cost |
| **Functions** (AWS Lambda with an adapter such as **Mangum**) | Event-driven, pay per request | Spiky, low-volume APIs (cold starts apply; WebSockets are limited) |

Before deploying, work through this checklist:

1. **Configuration** comes from environment variables; no secrets in the repository.
2. **HTTPS** is enabled (most platforms provide certificates automatically).
3. **Database** is a managed service with backups, and **migrations** (Lesson 13) run as a controlled step, for example `alembic upgrade head` in a release command rather than in every app instance on startup.
4. **CORS** allows only your real front-end origins (Lesson 15).
5. **Docs** are disabled or protected if the API is private.
6. **Logging** goes to standard output so the platform can collect it; consider structured (JSON) logs.
7. **Health check** endpoint exists and is configured in the platform.
8. **Workers/replicas** are sized after load testing, and database connection limits are respected (Lesson 13.3).
9. **Monitoring and alerting** are set up (error tracking such as Sentry, metrics with Prometheus or your cloud's tools).
10. **CI/CD** runs tests and deploys automatically from the main branch.

A minimal GitHub Actions workflow that runs tests on every push:

```yaml
name: tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt pytest httpx
      - run: pytest
```

Deploy in small steps: start with one simple platform, add monitoring, and only move to more complex infrastructure when you actually need it.

---

[Previous](./[17]-OpenAPI-And-Documentation.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[19]-Next-Steps-With-FastAPI.md)
