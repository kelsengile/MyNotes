[Previous](./[17]-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[19]-Deployment.md)

*Quality And Delivery*

# Lesson 18 - Settings And Environments

---

## 18.1 Splitting settings

### The problem with one settings file

A new project has a single `settings.py`. This quickly becomes a problem because the same code runs in several **environments**, and each needs different values:

| Setting | Development | Testing | Production |
|---|---|---|---|
| `DEBUG` | `True` | `False` | `False` |
| Database | SQLite file | In-memory SQLite | PostgreSQL |
| Email | Print to console | In-memory | Real SMTP server |
| `ALLOWED_HOSTS` | `[]` or localhost | `[]` | Real domain names |
| Cache | Local memory | Dummy | Redis |
| HTTPS settings | Off | Off | On |

Constantly editing one file by hand (and forgetting to switch `DEBUG` back) is dangerous. The solution is to **split settings** into several files that share a common base.

### Convert settings.py into a package

Replace `mysite/settings.py` with a folder:

```
mysite/
├── __init__.py
├── settings/
│   ├── __init__.py
│   ├── base.py          # shared by every environment
│   ├── development.py   # local development
│   ├── testing.py       # automated tests
│   └── production.py    # live site
├── urls.py
├── asgi.py
└── wsgi.py
```

Move the old `settings.py` into `settings/base.py`. Because the file is now one level deeper, **fix `BASE_DIR`**:

```python
# mysite/settings/base.py
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent.parent    # three .parent calls now
```

### base.py: the shared part

`base.py` holds everything common: `INSTALLED_APPS`, `MIDDLEWARE`, `TEMPLATES`, `ROOT_URLCONF`, password validators, internationalization, static settings, and so on. It should **not** contain `DEBUG = True` or secrets.

```python
# mysite/settings/base.py
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent.parent

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "blog",
]

MIDDLEWARE = [...]
ROOT_URLCONF = "mysite.urls"
TEMPLATES = [...]
AUTH_PASSWORD_VALIDATORS = [...]

STATIC_URL = "static/"
STATICFILES_DIRS = [BASE_DIR / "static"]
STATIC_ROOT = BASE_DIR / "staticfiles"
```

### development.py

Import everything from the base, then override:

```python
# mysite/settings/development.py
from .base import *          # noqa: F401,F403

DEBUG = True
ALLOWED_HOSTS = ["localhost", "127.0.0.1"]

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}

EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
```

### testing.py

```python
# mysite/settings/testing.py
from .base import *          # noqa: F401,F403

DEBUG = False
DATABASES = {"default": {"ENGINE": "django.db.backends.sqlite3", "NAME": ":memory:"}}
PASSWORD_HASHERS = ["django.contrib.auth.hashers.MD5PasswordHasher"]    # fast hashing for tests ONLY
EMAIL_BACKEND = "django.core.mail.backends.locmem.EmailBackend"
CACHES = {"default": {"BACKEND": "django.core.cache.backends.dummy.DummyCache"}}
```

The weak hasher above is acceptable **only** in test settings; it makes tests much faster.

### production.py

```python
# mysite/settings/production.py
import os
from .base import *          # noqa: F401,F403

DEBUG = False
ALLOWED_HOSTS = os.environ["DJANGO_ALLOWED_HOSTS"].split(",")

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.environ["DB_NAME"],
        "USER": os.environ["DB_USER"],
        "PASSWORD": os.environ["DB_PASSWORD"],
        "HOST": os.environ["DB_HOST"],
        "PORT": os.environ.get("DB_PORT", "5432"),
    }
}

SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_HSTS_SECONDS = 31536000
SECURE_PROXY_SSL_HEADER = ("HTTP_X_FORWARDED_PROTO", "https")
```

### Choosing which file Django uses

Django reads the `DJANGO_SETTINGS_MODULE` environment variable. Three files mention a default and should be updated to the new path: `manage.py`, `wsgi.py`, and `asgi.py`.

```python
# manage.py  (default for local work)
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings.development")
```

```python
# wsgi.py and asgi.py  (default for servers)
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings.production")
```

Because `setdefault` only applies if the variable is not already set, you can override it from the command line:

```bash
export DJANGO_SETTINGS_MODULE=mysite.settings.testing
python manage.py test

# or for a single command
python manage.py test --settings=mysite.settings.testing
```

### Pitfalls

- `from .base import *` imports names but not later changes: if you want to **extend** a list rather than replace it, do it explicitly:

```python
from .base import *
INSTALLED_APPS += ["debug_toolbar"]
MIDDLEWARE.insert(0, "debug_toolbar.middleware.DebugToolbarMiddleware")
```

- Keep differences **small**. The more development and production diverge, the more bugs appear only after deployment.
- Do not create a separate `local_settings.py` that is imported conditionally and untracked; explicit environment files are easier to reason about.
- Never default to production-unsafe values in `base.py` (for example `DEBUG = True`).

### The alternative: one file driven by environment variables

Many modern projects keep a **single** settings file and let environment variables change behavior (Section 18.2). Both approaches are valid. A split structure is clearest when environments differ a lot; the single-file approach is simplest when they differ only in values.

---

## 18.2 Environment variables

### What is an environment variable?

An **environment variable** is a named value provided to a program by the operating system or the process that starts it. Python reads them from `os.environ`:

```python
import os

os.environ["HOME"]                    # raises KeyError if missing
os.environ.get("HOME")                # returns None if missing
os.environ.get("PORT", "8000")        # returns a default if missing
```

Environment variables are the standard way to separate **configuration** (which varies per environment) from **code** (which stays the same).

### Setting them

```bash
# macOS / Linux (for the current terminal session)
export DJANGO_SECRET_KEY="a-long-random-string"
export DJANGO_DEBUG="True"

# one-off for a single command
DJANGO_DEBUG=True python manage.py runserver

# Windows PowerShell
$env:DJANGO_SECRET_KEY = "a-long-random-string"

# Windows Command Prompt
set DJANGO_SECRET_KEY=a-long-random-string
```

### Reading them in settings

```python
# settings.py
import os

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]
DEBUG = os.environ.get("DJANGO_DEBUG", "False").lower() in ("1", "true", "yes")
ALLOWED_HOSTS = os.environ.get("DJANGO_ALLOWED_HOSTS", "").split(",")
```

**Remember:** every environment variable is a **string**. The text `"False"` is truthy in Python, so `DEBUG = os.environ.get("DEBUG")` would turn debugging **on** even when you wrote `False`. Always convert explicitly:

```python
DEBUG = os.environ.get("DJANGO_DEBUG", "False") == "True"        # a boolean
PORT = int(os.environ.get("PORT", "8000"))                         # an integer
ALLOWED_HOSTS = [h for h in os.environ.get("DJANGO_ALLOWED_HOSTS", "").split(",") if h]   # a list
```

### Required vs optional values

| Pattern | Behavior | Use for |
|---|---|---|
| `os.environ["NAME"]` | Raises `KeyError` immediately if missing | Required secrets (`SECRET_KEY`, database password) |
| `os.environ.get("NAME", default)` | Falls back to a default | Optional values with a safe default |

Failing fast is a good thing: a missing secret should stop the application at startup instead of letting it run misconfigured.

### Helper packages

Hand-written conversion code gets repetitive. Popular helpers:

| Package | Style |
|---|---|
| `django-environ` | Typed reads, database URL parsing |
| `python-decouple` | Simple `config("NAME", cast=bool)` |
| `python-dotenv` | Only loads a `.env` file into `os.environ` |

django-environ example:

```bash
pip install django-environ
```

```python
# settings.py
from pathlib import Path
import environ

BASE_DIR = Path(__file__).resolve().parent.parent

env = environ.Env(
    DEBUG=(bool, False),                 # (type, default)
    ALLOWED_HOSTS=(list, []),
)
environ.Env.read_env(BASE_DIR / ".env")  # read the file if present; real env vars still win

SECRET_KEY = env("SECRET_KEY")
DEBUG = env("DEBUG")
ALLOWED_HOSTS = env("ALLOWED_HOSTS")
DATABASES = {"default": env.db("DATABASE_URL")}      # parses a URL into Django's dictionary
```

The matching `.env` file:

```bash
SECRET_KEY=change-me
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
DATABASE_URL=postgres://user:password@localhost:5432/mydb
```

### The DATABASE_URL convention

Many hosting platforms provide the database connection as a single URL:

```
postgres://USER:PASSWORD@HOST:PORT/NAME
```

Packages like `dj-database-url` or `django-environ` convert it to Django's `DATABASES` format, making your project portable between hosts.

```python
import dj_database_url

DATABASES = {"default": dj_database_url.config(default="sqlite:///db.sqlite3", conn_max_age=600)}
```

### Good practices

- Keep `.env` in `.gitignore`, and commit a `.env.example` with placeholders (Lesson 17).
- Use **clear, prefixed names** like `DJANGO_SECRET_KEY` to avoid clashes with other programs.
- Document every variable your project needs in the README.
- In production, set variables through your host or secret manager rather than a file on disk.
- Real environment variables should take priority over a `.env` file.

### What should be an environment variable?

| Put in the environment | Keep in code |
|---|---|
| Secrets (keys, passwords, tokens) | `INSTALLED_APPS` |
| Anything that differs between environments (hosts, database URL, email server) | `MIDDLEWARE`, `TEMPLATES` |
| Feature switches that operations staff may change | URL structure, business rules |

---

## 18.3 Debug mode and logging

### What DEBUG does

`DEBUG = True` is for development. It changes Django's behavior in several ways:

- Errors show a detailed **debug page** with the traceback, local variables, settings, and SQL queries.
- Static files are served automatically by `runserver` (Lesson 14).
- `ALLOWED_HOSTS` may be empty when running locally.
- Django records every SQL query in `connection.queries`, which uses extra **memory**.
- Custom error templates such as `404.html` are **not** shown (Lesson 3).

With `DEBUG = False`, users see your friendly error pages and you must configure other ways of learning about errors.

**Never run production with `DEBUG = True`.** It leaks source code, configuration, and data, and it can leak memory over time.

### Where do errors go in production?

With debugging off, you need two things: a way to **see** errors and a record of what happened. Django supports both:

1. **Logging**: writing messages to the console, files, or services.
2. **Error reporting**: email to `ADMINS` or a monitoring service like Sentry.

### Python's logging in 60 seconds

Python's `logging` module has four main concepts:

| Concept | Role |
|---|---|
| **Logger** | The named object your code calls (`logger.warning("...")`) |
| **Level** | Severity: `DEBUG` < `INFO` < `WARNING` < `ERROR` < `CRITICAL` |
| **Handler** | Where messages go (console, file, email, external service) |
| **Formatter** | How each message looks |

Using a logger in your code:

```python
# blog/views.py
import logging

logger = logging.getLogger(__name__)      # the logger is named after the module, "blog.views"


def post_detail(request, pk):
    logger.debug("Looking up post %s", pk)
    try:
        post = Post.objects.get(pk=pk)
    except Post.DoesNotExist:
        logger.warning("Post %s was requested but does not exist", pk)
        raise Http404
    ...
```

Pass values as **arguments** (`"%s", pk`) rather than formatting the string yourself; logging only formats the message if it will actually be emitted. Use `logger.exception("...")` inside an `except` block to include the traceback.

Prefer `logging` over `print()`: it offers levels, destinations, and timestamps, and works properly with servers.

### Configuring logging in Django

Use the `LOGGING` setting, which uses Python's `dictConfig` format:

```python
# settings.py
LOGGING = {
    "version": 1,
    "disable_existing_loggers": False,

    "formatters": {
        "verbose": {
            "format": "{levelname} {asctime} {name} {process:d} {message}",
            "style": "{",
        },
        "simple": {"format": "{levelname} {message}", "style": "{"},
    },

    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "formatter": "verbose",
        },
        "file": {
            "class": "logging.handlers.RotatingFileHandler",
            "filename": BASE_DIR / "logs" / "django.log",
            "maxBytes": 5 * 1024 * 1024,
            "backupCount": 5,
            "formatter": "verbose",
        },
        "mail_admins": {
            "class": "django.utils.log.AdminEmailHandler",
            "level": "ERROR",
        },
    },

    "root": {
        "handlers": ["console"],
        "level": "WARNING",
    },

    "loggers": {
        "django": {
            "handlers": ["console"],
            "level": "INFO",
            "propagate": False,
        },
        "django.request": {
            "handlers": ["console", "mail_admins"],
            "level": "ERROR",
            "propagate": False,
        },
        "blog": {
            "handlers": ["console", "file"],
            "level": "DEBUG",
            "propagate": False,
        },
    },
}
```

Notes:

- `"disable_existing_loggers": False` keeps Django's own loggers working.
- The `"blog"` logger covers every module whose name starts with `blog.` (such as `blog.views`), because logger names form a hierarchy.
- `RotatingFileHandler` creates a new file when the old one reaches `maxBytes`, preventing logs from filling the disk. Create the `logs/` folder first.
- Set `LOG_LEVEL` through an environment variable so you can change verbosity without editing code:

```python
"level": os.environ.get("DJANGO_LOG_LEVEL", "INFO"),
```

### Logging to the console in containers

On platforms like Docker, Heroku, and Kubernetes, the recommended approach is to log to **standard output** (the console handler). The platform collects it. Avoid writing log files inside containers.

### Emailing errors and using Sentry

```python
ADMINS = [("Ada", "ada@example.com")]
SERVER_EMAIL = "errors@example.com"
```

With the `mail_admins` handler above, each server error (500) is emailed to `ADMINS`. For real projects, **Sentry** is more practical: it groups duplicate errors, shows context, and sends alerts.

```bash
pip install sentry-sdk
```

```python
import sentry_sdk

sentry_sdk.init(
    dsn=os.environ.get("SENTRY_DSN"),
    traces_sample_rate=0.1,
    send_default_pii=False,
)
```

### Debugging tools for development

| Tool | Purpose |
|---|---|
| `django-debug-toolbar` | A browser panel showing queries, templates, timing, and settings |
| `django-extensions` | `shell_plus` and `runserver_plus` with an interactive debugger |
| `breakpoint()` | Drops into Python's `pdb` debugger at that line |

### What not to log

Never log passwords, full credit card numbers, tokens, session IDs, or other personal data. Logs are often copied to many systems and kept for a long time.

---

## 18.4 Twelve-factor configuration

### What is the twelve-factor app?

The **Twelve-Factor App** is a set of principles for building applications that are easy to deploy, scale, and maintain, originally written by developers at Heroku. It is widely used as a checklist for modern web apps. Several factors map directly onto Django settings.

### The twelve factors at a glance

| # | Factor | In a Django project |
|---|---|---|
| 1 | **Codebase**: one codebase in version control, many deploys | One Git repository deployed to development, staging, and production |
| 2 | **Dependencies**: declare and isolate them | `requirements.txt` and a virtual environment (Lesson 1) |
| 3 | **Config**: store config in the environment | `SECRET_KEY`, database URL, and hosts from environment variables |
| 4 | **Backing services**: treat them as attached resources | Database, cache, and email addressed by URLs and swappable via settings |
| 5 | **Build, release, run**: separate stages | `collectstatic` and install at build; `migrate` at release; Gunicorn at run |
| 6 | **Processes**: run as stateless processes | No local file or memory state; use the database, cache, and cloud storage |
| 7 | **Port binding**: export services by port | Gunicorn listens on a port; a proxy forwards traffic |
| 8 | **Concurrency**: scale out via the process model | Run more Gunicorn workers or more containers |
| 9 | **Disposability**: fast start, graceful shutdown | Stateless containers that can be killed and replaced |
| 10 | **Dev/prod parity**: keep environments similar | Use PostgreSQL locally too, perhaps through Docker |
| 11 | **Logs**: treat logs as event streams | Log to stdout; let the platform collect them |
| 12 | **Admin processes**: run one-off tasks in the same environment | `migrate`, `createsuperuser`, and management commands run with the same code and config |

### Factor 3 in detail: config in the environment

This is the factor Django developers use most. The test of whether configuration is separated correctly: **could you open-source your repository right now without leaking any credentials?** If yes, your config is properly separated.

Config means anything that **varies between deploys**: resource handles for databases and caches, credentials for external services, and per-deploy values such as the canonical hostname. It does **not** include internal application structure like `INSTALLED_APPS`.

### Applying the principles to a Django project

A twelve-factor-style settings file:

```python
# mysite/settings.py
import os
from pathlib import Path

import dj_database_url

BASE_DIR = Path(__file__).resolve().parent.parent

# Factor 3: config from the environment
SECRET_KEY = os.environ["SECRET_KEY"]
DEBUG = os.environ.get("DEBUG", "False") == "True"
ALLOWED_HOSTS = [h for h in os.environ.get("ALLOWED_HOSTS", "").split(",") if h]

# Factor 4: backing services as attached resources
DATABASES = {"default": dj_database_url.config(env="DATABASE_URL", conn_max_age=600)}
CACHES = {"default": {"BACKEND": "django.core.cache.backends.redis.RedisCache",
                      "LOCATION": os.environ.get("REDIS_URL", "redis://127.0.0.1:6379/1")}}

# Factor 11: logs as event streams
LOGGING = {
    "version": 1,
    "disable_existing_loggers": False,
    "handlers": {"console": {"class": "logging.StreamHandler"}},
    "root": {"handlers": ["console"], "level": os.environ.get("LOG_LEVEL", "INFO")},
}
```

### Factor 6: stateless processes

Because any process may be stopped and replaced, it must not depend on local state:

- Do not store uploaded files on the server's disk. Use cloud storage (Lesson 14).
- Do not keep sessions or caches in process memory. Use a database, Redis, or Memcached (Lesson 13).
- Do not rely on `LocMemCache` in production. Each worker has its own separate copy.
- Do not rely on files written by one request being readable by the next.

### Factor 10: dev/prod parity

Differences between environments cause "works on my machine" bugs. Using SQLite locally and PostgreSQL in production hides issues such as differences in text matching, transactions, and locking. Run the same database engine everywhere, easily done with Docker (Lesson 19):

```yaml
# docker-compose.yml (development)
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: devpassword
    ports: ["5432:5432"]
```

### Factor 5: build, release, run

| Stage | What happens | Django commands |
|---|---|---|
| **Build** | Create a deployable artifact | `pip install -r requirements.txt`, `collectstatic` |
| **Release** | Combine the build with config | Set environment variables; `migrate` |
| **Run** | Start the processes | `gunicorn mysite.wsgi` |

A release should be **immutable**: to change anything, create a new release rather than editing files on a running server.

### Factor 12: admin processes

One-off tasks (migrations, data fixes, creating users) should run from the **same codebase and configuration** as the app:

```bash
python manage.py migrate
python manage.py createsuperuser
python manage.py my_custom_command
```

### A twelve-factor checklist for Django

- [ ] All secrets come from environment variables.
- [ ] No `.env` or credentials in version control.
- [ ] `requirements.txt` is pinned and complete.
- [ ] Database and cache are configured by URL.
- [ ] Logs go to standard output.
- [ ] Uploaded files go to external storage.
- [ ] The same code runs in every environment; only config differs.
- [ ] Migrations and static collection are part of the deployment pipeline.
- [ ] The app can be stopped and restarted at any time with no data loss.

These habits make deployment (the next lesson) far smoother.

---

[Previous](./[17]-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[19]-Deployment.md)
