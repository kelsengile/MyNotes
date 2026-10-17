[Previous](./[1]-What-Is-Django.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[3]-URLs-And-Views.md)

*Basics*

# Lesson 2 - Projects And Apps

---

## 2.1 Project vs app

Django separates code into two levels: **projects** and **apps**. Confusing the two is one of the most common beginner mistakes.

- A **project** is the entire website. It holds configuration (`settings.py`), the root URLs, and the entry points for servers. A project is created once.
- An **app** is a self-contained module that does one thing: a blog, a comment system, a shopping cart, a user profile area. A project contains one or more apps.

```
mysite (project)
├── blog          (app: posts and categories)
├── accounts      (app: registration and profiles)
└── shop          (app: products and orders)
```

### Why split into apps?

- **Organization** — each app has a single, clear responsibility.
- **Reusability** — a well-designed app can be dropped into other projects. Many third-party packages (like Django REST Framework) are simply Django apps.
- **Maintainability** — smaller modules are easier to understand, test, and change.

A good rule of thumb: if you can describe a feature in one short sentence without using "and", it is probably a good app. Do not worry about getting this perfect up front; you can always refactor later.

---

## 2.2 Creating and registering apps

### Creating an app

Run this from the folder containing `manage.py`:

```bash
python manage.py startapp blog
```

Django generates:

```
blog/
├── __init__.py
├── admin.py
├── apps.py
├── migrations/
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```

| File | Purpose |
|---|---|
| `admin.py` | Registers models with the Django admin (Lesson 9) |
| `apps.py` | The app's configuration class |
| `migrations/` | Database schema history (Lesson 6) |
| `models.py` | Database models (Lesson 5) |
| `tests.py` | Automated tests (Lesson 16) |
| `views.py` | Request-handling logic (Lesson 3) |

You will typically add your own files later, such as `urls.py`, `forms.py`, and a `templates/` folder.

### Registering the app

Creating an app is not enough. Django only knows about apps listed in the `INSTALLED_APPS` setting. Open `mysite/settings.py` and add yours:

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Local apps
    "blog",
]
```

You may also see the longer form `"blog.apps.BlogConfig"`, which points directly at the configuration class in `apps.py`. Both work; the short name is enough for most projects because Django finds the config automatically.

If you forget to register an app, its models will not be picked up by migrations, its templates may not be found, and its admin registrations will not appear.

### The app configuration class

`apps.py` contains a small class like this:

```python
from django.apps import AppConfig


class BlogConfig(AppConfig):
    default_auto_field = "django.db.models.BigAutoField"
    name = "blog"
```

You will use this class later to run startup code, such as connecting signals (Lesson 13).

---

## 2.3 Settings overview

`mysite/settings.py` is a plain Python file full of variables. Django reads these at startup. Here are the most important ones.

### BASE_DIR

```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent
```

The absolute path to your project root. Use it to build other paths reliably, for example `BASE_DIR / "templates"`.

### SECRET_KEY

A long random string used for cryptographic signing (sessions, password reset tokens, and more). It must be kept **private** and never committed to a public repository. Lesson 17 and Lesson 18 explain how to load it from an environment variable.

### DEBUG

```python
DEBUG = True
```

When `True`, Django shows detailed error pages and serves static files automatically in development. It must be `False` in production, because debug pages can leak sensitive information.

### ALLOWED_HOSTS

A list of host names your site may serve. It can stay empty during development with `DEBUG = True`, but must be set in production:

```python
ALLOWED_HOSTS = ["example.com", "www.example.com"]
```

### INSTALLED_APPS and MIDDLEWARE

`INSTALLED_APPS` lists the active apps (covered above). `MIDDLEWARE` lists the processing layers every request passes through, in order (Lesson 13).

### ROOT_URLCONF

The Python path to your root URL configuration:

```python
ROOT_URLCONF = "mysite.urls"
```

### TEMPLATES

Configures the template engine, including where to look for templates (Lesson 4):

```python
TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [BASE_DIR / "templates"],
        "APP_DIRS": True,
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",
            ],
        },
    },
]
```

### DATABASES

By default Django uses SQLite, a file-based database that needs no setup:

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}
```

For production you will usually switch to PostgreSQL (Lesson 19).

### Internationalization and time

```python
LANGUAGE_CODE = "en-us"
TIME_ZONE = "UTC"
USE_I18N = True
USE_TZ = True
```

Keeping `USE_TZ = True` is recommended: Django stores datetimes in UTC and converts them to local time for display.

### STATIC_URL and DEFAULT_AUTO_FIELD

`STATIC_URL` is the URL prefix for static files (Lesson 14). `DEFAULT_AUTO_FIELD` sets the type of the automatic primary key on models (Lesson 5).

---

## 2.4 Running the development server

### Starting the server

```bash
python manage.py runserver
```

By default it listens on `http://127.0.0.1:8000/`. You can change the port or address:

```bash
python manage.py runserver 8080            # different port
python manage.py runserver 0.0.0.0:8000    # reachable from other devices on your network
```

If you use `0.0.0.0`, remember to add the relevant host name or IP to `ALLOWED_HOSTS`.

### Auto-reload

The development server automatically restarts when you change Python files, so you rarely need to stop and start it manually. It does **not** pick up changes to some things (like new files in a brand-new `templates` folder in rare cases); if something seems stuck, stop the server with `Ctrl + C` and start it again.

### Not for production

The built-in server is designed for convenience, not performance or security. Never use it to serve a real website. Lesson 19 covers production servers such as Gunicorn.

### Other useful `manage.py` commands

| Command | Purpose |
|---|---|
| `python manage.py check` | Looks for common problems in your project |
| `python manage.py migrate` | Applies database migrations |
| `python manage.py makemigrations` | Creates new migration files |
| `python manage.py createsuperuser` | Creates an admin account |
| `python manage.py shell` | Opens a Python shell with Django loaded |
| `python manage.py test` | Runs your test suite |
| `python manage.py collectstatic` | Gathers static files for deployment |

Run `python manage.py help` to see every available command.

---

[Previous](./[1]-What-Is-Django.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[3]-URLs-And-Views.md)
