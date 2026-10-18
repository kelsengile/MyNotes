[Previous](./[5]-Forms-With-Flask-WTF.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[7]-Configuration-And-Environments.md)

*Building Applications*

# Lesson 6 - Application Factory And Blueprints

---

## 6.1 Why Structure Matters

A single `app.py` is perfect for learning. As a project grows, that one file becomes crowded with routes, models, forms, and configuration. Problems appear:

- **Hard to navigate** — hundreds of lines mixing unrelated features.
- **Circular imports** — `models.py` imports `app`, while `app.py` imports `models`.
- **Hard to test** — a single global `app` is created at import time, so you cannot easily give tests a different configuration.
- **Hard to reuse** — you cannot run two differently configured copies of the app.

Two Flask tools solve these problems:

| Tool | What it solves |
|---|---|
| **Application factory** | Creates the app inside a function, so configuration and extensions are set up on demand. |
| **Blueprints** | Split routes and views into self-contained feature modules. |

### The single-file style (before)

```python
# app.py  - everything in one place
from flask import Flask

app = Flask(__name__)
app.config["SECRET_KEY"] = "dev"

@app.route("/")
def home():
    return "Home"

@app.route("/login")
def login():
    return "Login"

@app.route("/admin")
def admin():
    return "Admin"
```

This works until there are dozens of routes, models, and settings. The rest of this lesson shows how to organize them.

---

## 6.2 The Application Factory Pattern

An **application factory** is a function that **creates and returns** a configured Flask app instead of building one at import time.

### A minimal factory

```python
# myapp/__init__.py
from flask import Flask


def create_app(config_object="config.DevelopmentConfig"):
    app = Flask(__name__)
    app.config.from_object(config_object)

    @app.route("/")
    def home():
        return "Hello from the factory!"

    return app
```

### Running a factory app

Flask automatically looks for a function named `create_app` (or `make_app`) inside the package you specify:

```bash
flask --app myapp run --debug
```

You can also pass arguments explicitly:

```bash
flask --app "myapp:create_app('config.DevelopmentConfig')" run --debug
```

### Extensions and `init_app`

Extensions such as Flask-SQLAlchemy or Flask-Login are normally created **without** an app, in a separate module, and attached later inside the factory with `init_app`. This avoids circular imports.

```python
# myapp/extensions.py
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager

db = SQLAlchemy()
login_manager = LoginManager()
```

```python
# myapp/__init__.py
from flask import Flask
from .extensions import db, login_manager


def create_app(config_object="config.DevelopmentConfig"):
    app = Flask(__name__)
    app.config.from_object(config_object)

    db.init_app(app)
    login_manager.init_app(app)

    return app
```

Any other module can now do `from .extensions import db` without importing the app.

### Accessing the app without importing it: `current_app`

Inside a request, other modules do not have your `app` variable. Flask provides a proxy named `current_app`:

```python
from flask import current_app


def log_startup_info():
    current_app.logger.info("Debug mode is %s", current_app.debug)
```

`current_app` only works while an **application context** is active (during a request, a CLI command, or inside `with app.app_context():`).

### Benefits of a factory

1. **Testing** — create an app per test with a test configuration (Lesson 15).
2. **Multiple configurations** — development, testing, and production share one codebase.
3. **No import-time side effects** — nothing runs until you call `create_app`.
4. **Fewer circular imports** — extensions are created independently.

---

## 6.3 Blueprints

A **blueprint** is a collection of routes, templates, and static files that describes one feature area. You register blueprints onto the app, much like plugging in modules.

### Creating a blueprint

```python
# myapp/auth/routes.py
from flask import Blueprint, render_template

auth_bp = Blueprint(
    "auth",                     # blueprint name, used in endpoints
    __name__,
    template_folder="templates",
)


@auth_bp.route("/login")
def login():
    return render_template("auth/login.html")


@auth_bp.route("/logout")
def logout():
    return "Logged out"
```

### Registering a blueprint

```python
# myapp/__init__.py
from flask import Flask


def create_app(config_object="config.DevelopmentConfig"):
    app = Flask(__name__)
    app.config.from_object(config_object)

    from .auth.routes import auth_bp
    from .blog.routes import blog_bp

    app.register_blueprint(auth_bp, url_prefix="/auth")
    app.register_blueprint(blog_bp, url_prefix="/blog")

    return app
```

With `url_prefix="/auth"`, the route `/login` is served at `/auth/login`.

### Endpoint names are namespaced

Because blueprints have names, their endpoints include the blueprint name:

```python
url_for("auth.login")      # /auth/login
url_for("blog.show_post", post_id=3)
```

Inside a template or a view of the **same** blueprint, you can use a leading dot as a shortcut:

```python
url_for(".login")          # same as "auth.login" when called inside the auth blueprint
```

### Blueprint-specific hooks and error handlers

```python
@auth_bp.before_request
def require_https_note():
    pass  # runs only for requests handled by this blueprint


@auth_bp.errorhandler(404)
def auth_not_found(error):
    return "Auth page not found", 404
```

Use `app_errorhandler` instead of `errorhandler` to register an error handler that applies to the **whole app** from within a blueprint.

### Templates in blueprints

Flask searches the app's `templates` folder first, then each blueprint's. To avoid name clashes, put blueprint templates in a subfolder named after the blueprint:

```
myapp/auth/templates/auth/login.html
```

and render with `render_template("auth/login.html")`.

---

## 6.4 Project Layout

There is no single correct structure, but a common and scalable one looks like this:

```
flask-project/
├── myapp/
│   ├── __init__.py          # create_app() lives here
│   ├── extensions.py        # db, login_manager, migrate, ...
│   ├── models.py            # database models
│   ├── auth/
│   │   ├── __init__.py
│   │   ├── routes.py        # auth blueprint
│   │   ├── forms.py
│   │   └── templates/
│   │       └── auth/
│   │           └── login.html
│   ├── blog/
│   │   ├── __init__.py
│   │   ├── routes.py        # blog blueprint
│   │   └── forms.py
│   ├── templates/
│   │   ├── base.html
│   │   └── errors/
│   │       └── 404.html
│   └── static/
│       ├── css/
│       └── js/
├── tests/
│   ├── conftest.py
│   └── test_auth.py
├── config.py                # configuration classes
├── requirements.txt
├── .env                     # secrets (never commit)
├── .gitignore
└── wsgi.py                  # production entry point
```

### Key files

`config.py`:

```python
import os


class Config:
    SECRET_KEY = os.environ.get("SECRET_KEY", "dev-only-key")
    SQLALCHEMY_DATABASE_URI = os.environ.get("DATABASE_URL", "sqlite:///app.db")


class DevelopmentConfig(Config):
    DEBUG = True


class ProductionConfig(Config):
    DEBUG = False
```

`wsgi.py`:

```python
from myapp import create_app

app = create_app("config.ProductionConfig")
```

Production servers use it like this:

```bash
gunicorn wsgi:app
```

### Layout approaches

| Approach | Description | Best for |
|---|---|---|
| **By feature** (shown above) | Each blueprint folder holds its own routes, forms, and templates. | Medium and large apps. |
| **By type** | One `routes/`, `forms/`, `models/` folder each. | Small and medium apps. |
| **Single file** | Everything in `app.py`. | Tiny apps and learning. |

### Tips

- Import blueprints **inside** `create_app` to avoid circular imports.
- Keep `models.py` free of imports from route modules.
- Start simple and split into blueprints once a feature has several routes.
- Pick one structure and stay consistent.

---

[Previous](./[5]-Forms-With-Flask-WTF.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[7]-Configuration-And-Environments.md)
