[Previous](./[18]-Settings-And-Environments.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[20]-Next-Steps-With-Django.md)

*Quality And Delivery*

# Lesson 19 - Deployment

---

## 19.1 WSGI and ASGI servers

### Why runserver is not enough

The `runserver` command from Lesson 2 is a development tool. It handles one kind of load poorly, has not been audited for security, and restarts itself constantly. A production site needs a proper **application server** that can run many worker processes, recover from crashes, and talk to a front-line web server.

### The two interfaces: WSGI and ASGI

Python web frameworks do not talk to the network directly. They follow a standard interface so any compatible server can run them.

| Interface | Stands for | Style | Django entry point |
|---|---|---|---|
| **WSGI** | Web Server Gateway Interface | Synchronous; one request per worker at a time | `mysite/wsgi.py` |
| **ASGI** | Asynchronous Server Gateway Interface | Supports async code, WebSockets, and long-lived connections | `mysite/asgi.py` |

`startproject` created both files for you (Lesson 1). Each exposes a variable named `application` that the server imports.

```python
# mysite/wsgi.py
import os
from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings.production")
application = get_wsgi_application()
```

Use **WSGI** for a traditional site (the most common and simplest choice). Use **ASGI** if you need WebSockets, server-sent events, or heavily async views (Lesson 20).

### Gunicorn (WSGI)

**Gunicorn** is the most popular production WSGI server for Django.

```bash
pip install gunicorn

gunicorn mysite.wsgi:application --bind 0.0.0.0:8000 --workers 3
```

| Option | Meaning |
|---|---|
| `mysite.wsgi:application` | Python path to the `application` object |
| `--bind 0.0.0.0:8000` | Address and port to listen on |
| `--workers 3` | Number of worker processes; a common starting point is `(2 x CPU cores) + 1` |
| `--timeout 30` | Seconds before a silent worker is killed |
| `--access-logfile -` | Write access logs to standard output |

Settings may be kept in a file, `gunicorn.conf.py`:

```python
# gunicorn.conf.py
bind = "0.0.0.0:8000"
workers = 3
timeout = 30
accesslog = "-"
errorlog = "-"
```

### Uvicorn and Daphne (ASGI)

```bash
pip install uvicorn

uvicorn mysite.asgi:application --host 0.0.0.0 --port 8000 --workers 3
```

A common production pattern is Gunicorn managing Uvicorn workers:

```bash
pip install uvicorn gunicorn
gunicorn mysite.asgi:application -k uvicorn.workers.UvicornWorker --workers 3
```

**Daphne** and **Hypercorn** are other ASGI servers. Windows users may prefer **Waitress** as a WSGI server, since Gunicorn does not run on Windows.

### The reverse proxy

Application servers should not face the internet directly. Put a **reverse proxy** such as **Nginx** in front. It:

- Terminates **HTTPS** and handles certificates.
- Serves **static and media files** extremely quickly.
- Buffers slow clients so workers are not tied up.
- Provides compression, caching, rate limiting, and security headers.

```
Browser  ->  Nginx (ports 80 / 443)  ->  Gunicorn (port 8000)  ->  Django
                   |
                   +-> /static/ and /media/ served directly from disk
```

A minimal Nginx configuration:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    client_max_body_size 10M;

    location /static/ {
        alias /srv/mysite/staticfiles/;
        expires 30d;
    }

    location /media/ {
        alias /srv/mysite/media/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

The `X-Forwarded-Proto` header lets Django know the original request used HTTPS, matching `SECURE_PROXY_SSL_HEADER` in Lesson 17. Free certificates are available from **Let's Encrypt** using the `certbot` tool.

### Keeping the server running

If Gunicorn crashes or the machine restarts, something must start it again. On Linux this is usually **systemd**:

```ini
# /etc/systemd/system/gunicorn.service
[Unit]
Description=Gunicorn for mysite
After=network.target

[Service]
User=deploy
WorkingDirectory=/srv/mysite
EnvironmentFile=/etc/mysite.env
ExecStart=/srv/mysite/.venv/bin/gunicorn mysite.wsgi:application --bind 127.0.0.1:8000 --workers 3
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now gunicorn
sudo systemctl status gunicorn
sudo systemctl restart gunicorn      # after deploying new code
```

### Platform-as-a-service alternative

Platforms such as **Render, Railway, Fly.io, and Heroku** manage the proxy, HTTPS, and process supervision for you. You supply a start command (usually the Gunicorn line above), environment variables, and a database. For beginners, a PaaS is often the fastest route to a live site.

---

## 19.2 Static files in production

### The situation

With `DEBUG = False`, `runserver` no longer serves static files (Lesson 14), and gunicorn does not either. If you deploy and the admin looks unstyled, this is the reason. You must **collect** the files and **serve** them with something else.

### Step 1: configure and collect

```python
# settings.py
STATIC_URL = "static/"
STATIC_ROOT = BASE_DIR / "staticfiles"
```

```bash
python manage.py collectstatic --noinput
```

Run this on every deployment, after installing dependencies and before starting the new version.

### Option A: Nginx serves them (traditional)

Point a `location /static/` block at `STATIC_ROOT`, as in the Nginx example in Section 19.1. This is the fastest approach and takes load off Python completely.

### Option B: WhiteNoise (simple and popular)

**WhiteNoise** lets Django serve its own static files efficiently, with compression and caching headers. It needs no separate web server for static files, making it ideal for platforms like Heroku, Render, and Railway.

```bash
pip install whitenoise
```

```python
# settings.py
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "whitenoise.middleware.WhiteNoiseMiddleware",       # directly after SecurityMiddleware
    "django.contrib.sessions.middleware.SessionMiddleware",
    ...
]

STORAGES = {
    "default": {"BACKEND": "django.core.files.storage.FileSystemStorage"},
    "staticfiles": {"BACKEND": "whitenoise.storage.CompressedManifestStaticFilesStorage"},
}
```

`CompressedManifestStaticFilesStorage` adds gzip/Brotli compression and **hashed file names** so browsers cache files for a long time and automatically fetch new versions after a change (Lesson 14).

WhiteNoise serves **static** files only. Media uploads still need a web server or cloud storage.

### Option C: CDN or object storage

For large or global audiences, upload static files to Amazon S3, Google Cloud Storage, or similar, and serve them through a **CDN** (Content Delivery Network) such as CloudFront or Cloudflare. A CDN caches your files on servers around the world, so users download them from a nearby location.

```python
STATIC_URL = "https://cdn.example.com/static/"
```

Or use `django-storages` as shown in Lesson 14.

### Media files in production

User uploads (`MEDIA_ROOT`) are a separate concern:

- With one server, Nginx can serve `/media/` from disk.
- With several servers or ephemeral containers, use cloud storage via `django-storages` (Lesson 14).
- Serve private files through a permission-checking view, not from a public folder.

### Cache headers

Files with hashed names can be cached **forever**, since a new version gets a new name:

```nginx
location /static/ {
    alias /srv/mysite/staticfiles/;
    add_header Cache-Control "public, max-age=31536000, immutable";
}
```

### Common problems

| Problem | Cause and fix |
|---|---|
| Admin or site has no CSS | `collectstatic` was not run, or nothing serves `/static/` |
| 404 for static files | `STATIC_ROOT` and the server's `alias` path do not match |
| `ValueError: Missing staticfiles manifest entry` | Manifest storage is on but `collectstatic` has not been run, or a template references a missing file |
| Old CSS still appears | Not using hashed names; or a CDN cached the old file and needs purging |
| Uploads vanish after redeploy | Files were stored on a temporary container disk; use cloud storage |

---

## 19.3 Databases and Docker

### Use a production database

SQLite is wonderful for development, but production sites should use a client-server database. **PostgreSQL** is the most popular and best-supported choice with Django. MySQL/MariaDB are also supported.

Why not SQLite in production? It allows only one writer at a time, lacks network access, and its file lives on a local disk that may be temporary.

### Configuring PostgreSQL

```bash
pip install "psycopg[binary]"
```

```python
# settings.py
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.environ["DB_NAME"],
        "USER": os.environ["DB_USER"],
        "PASSWORD": os.environ["DB_PASSWORD"],
        "HOST": os.environ.get("DB_HOST", "localhost"),
        "PORT": os.environ.get("DB_PORT", "5432"),
        "CONN_MAX_AGE": 600,                 # reuse connections for up to 10 minutes
    }
}
```

Or from a single URL (Lesson 18):

```python
import dj_database_url
DATABASES = {"default": dj_database_url.config(conn_max_age=600)}
```

Then apply migrations on the new database:

```bash
python manage.py migrate
```

### Database good practices

- Keep credentials in environment variables.
- Use a managed database service (Amazon RDS, Google Cloud SQL, Neon, Supabase, Render, and others) so backups and updates are handled for you.
- Take **automated backups** and test restoring them.
- Run migrations as a deliberate step in each release (Lesson 6), and prefer small, reversible changes.
- Use the same database engine in development to avoid surprises (Lesson 18).

### Containers with Docker

**Docker** packages your application and everything it needs (Python, libraries, system packages) into an **image** that runs identically on any machine. This removes "it works on my machine" problems and makes deployment repeatable.

| Term | Meaning |
|---|---|
| **Image** | A read-only template containing your app and its dependencies |
| **Container** | A running instance of an image |
| **Dockerfile** | Instructions for building an image |
| **Docker Compose** | A tool for running several containers together (app, database, cache) |

### A Dockerfile for Django

```dockerfile
# Dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependencies first so this layer is cached when only code changes
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# Collect static files at build time (needs a throwaway key)
RUN DJANGO_SECRET_KEY=build-only python manage.py collectstatic --noinput

# Do not run as root
RUN useradd --create-home appuser
USER appuser

EXPOSE 8000
CMD ["gunicorn", "mysite.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "3"]
```

Add a `.dockerignore` so unneeded and sensitive files stay out of the image:

```
.venv/
.git/
.env
__pycache__/
*.sqlite3
media/
staticfiles/
```

Build and run:

```bash
docker build -t mysite .
docker run -p 8000:8000 --env-file .env mysite
```

### Docker Compose: app plus database

```yaml
# docker-compose.yml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: mysite
      POSTGRES_USER: mysite
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U mysite"]
      interval: 5s
      retries: 5

  web:
    build: .
    command: gunicorn mysite.wsgi:application --bind 0.0.0.0:8000
    env_file: .env
    environment:
      DB_HOST: db
      DB_NAME: mysite
      DB_USER: mysite
    ports:
      - "8000:8000"
    depends_on:
      db:
        condition: service_healthy

volumes:
  pgdata:
```

```bash
docker compose up --build
docker compose exec web python manage.py migrate
docker compose exec web python manage.py createsuperuser
```

Notes:

- A **volume** (`pgdata`) keeps database files when the container is replaced. Without it, you lose the data.
- Inside Compose, containers reach each other by **service name** (`DB_HOST: db`).
- Never put real secrets in `docker-compose.yml` that you commit; reference environment variables or `.env`.

### Running migrations in containers

Run `migrate` as a one-off release step, not at the start of every web container when several run at once:

```bash
docker compose run --rm web python manage.py migrate
```

### Docker best practices

- Use a slim base image and pin its version.
- Order Dockerfile steps so rarely changing layers (dependencies) come first.
- Run as a **non-root** user.
- Keep images small and free of secrets.
- Log to standard output (Lesson 18).
- Keep containers **stateless**: databases, cache, and uploads live outside them.

---

## 19.4 CI/CD basics

### What are CI and CD?

| Term | Meaning | Goal |
|---|---|---|
| **Continuous Integration (CI)** | Every push automatically builds the project and runs tests and checks | Catch problems immediately |
| **Continuous Delivery (CD)** | Every passing change is automatically prepared for release | Always have a deployable version |
| **Continuous Deployment** | Every passing change is automatically deployed to production | Fast, frequent releases |

Without automation, deployment depends on someone remembering every step. With it, the process is the same every time.

### A CI pipeline for Django

A typical pipeline, triggered on every push and pull request:

1. Check out the code.
2. Install Python and dependencies.
3. Run linters and formatters.
4. Check for missing migrations.
5. Run the test suite (with coverage).
6. Run security checks.

### GitHub Actions example

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: test_db
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
        ports: ["5432:5432"]
        options: >-
          --health-cmd pg_isready
          --health-interval 5s
          --health-retries 5

    env:
      DJANGO_SETTINGS_MODULE: mysite.settings.testing
      DJANGO_SECRET_KEY: ci-only-secret-key
      DATABASE_URL: postgres://postgres:postgres@localhost:5432/test_db

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: pip

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Lint
        run: ruff check .

      - name: Check for missing migrations
        run: python manage.py makemigrations --check --dry-run

      - name: Run tests with coverage
        run: |
          coverage run manage.py test
          coverage report --fail-under=80

      - name: Dependency vulnerability scan
        run: pip-audit -r requirements.txt

      - name: Deployment security checks
        run: python manage.py check --deploy --fail-level WARNING
        env:
          DJANGO_SETTINGS_MODULE: mysite.settings.production
          DJANGO_SECRET_KEY: a-long-random-ci-only-key-for-checks-0123456789abcdef
          DJANGO_ALLOWED_HOSTS: example.com
          DB_NAME: x
          DB_USER: x
          DB_PASSWORD: x
          DB_HOST: localhost
```

`makemigrations --check` fails if you changed a model but forgot to commit its migration (Lesson 6). Secrets for real services belong in GitHub **encrypted secrets** (`${{ secrets.NAME }}`), never in the file.

### Adding a deployment (CD) job

A simplified example that deploys over SSH after tests pass on `main`:

```yaml
  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to server
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            cd /srv/mysite
            git pull origin main
            source .venv/bin/activate
            pip install -r requirements.txt
            python manage.py migrate --noinput
            python manage.py collectstatic --noinput
            sudo systemctl restart gunicorn
```

Platforms like Render, Railway, Fly.io, and Heroku can deploy automatically when you push to a branch, so you may not need a separate deploy job.

### The standard release steps

Whichever tool you use, a Django release usually follows this order:

1. Install or build dependencies (`pip install -r requirements.txt`, or build the Docker image).
2. `python manage.py collectstatic --noinput`
3. `python manage.py migrate --noinput`
4. Restart the application server so it loads the new code.
5. Run a **smoke test**: request a key page or a `/health/` endpoint to confirm the site is up.

### A simple health-check endpoint

```python
# mysite/views.py
from django.db import connection
from django.http import JsonResponse


def health(request):
    try:
        with connection.cursor() as cursor:
            cursor.execute("SELECT 1")
    except Exception:
        return JsonResponse({"status": "error"}, status=503)
    return JsonResponse({"status": "ok"})
```

Load balancers and uptime monitors can call this to know whether the app is healthy.

### Safe migrations during deployment

- Make migrations **backward compatible** where possible, so old and new code can both run during a rolling release.
- Split risky changes (Lesson 6): add a column as nullable, deploy code that uses it, backfill data, then make it required in a later release.
- Always back up before running destructive migrations.

### Rollbacks

Know how to go back before you need to:

- Keep previous images or releases so you can redeploy the last good version.
- Remember that code can be rolled back instantly, but **database migrations may not be reversible** (Lesson 6). This is another reason to keep migrations small and compatible.

### Deployment checklist

- [ ] `DEBUG = False` and `ALLOWED_HOSTS` set (Lesson 17).
- [ ] `python manage.py check --deploy` passes.
- [ ] All secrets come from the environment (Lesson 18).
- [ ] Production database configured, migrated, and backed up.
- [ ] Static files collected and served; media on suitable storage.
- [ ] HTTPS enabled with a valid certificate and HSTS configured.
- [ ] Application server (Gunicorn/Uvicorn) behind a reverse proxy, restarted automatically on failure.
- [ ] Logging and error monitoring configured (Lesson 18).
- [ ] CI runs tests and checks on every change.
- [ ] A rollback plan exists.

---

[Previous](./[18]-Settings-And-Environments.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[20]-Next-Steps-With-Django.md)
