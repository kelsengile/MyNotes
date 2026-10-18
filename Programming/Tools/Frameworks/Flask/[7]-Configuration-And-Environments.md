[Previous](./[6]-Application-Factory-And-Blueprints.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[8]-Sessions-And-Cookies.md)

*Building Applications*

# Lesson 7 - Configuration And Environments

---

## 7.1 Config Objects

Every Flask app has a `config` object, which behaves like a dictionary. It stores settings used by Flask itself (such as `DEBUG` and `SECRET_KEY`) and by extensions (such as `SQLALCHEMY_DATABASE_URI`).

### Setting values directly

```python
from flask import Flask

app = Flask(__name__)
app.config["SECRET_KEY"] = "dev-only-key"
app.config["DEBUG"] = True
app.config.update(
    MAX_CONTENT_LENGTH=2 * 1024 * 1024,   # 2 MB upload limit
    JSON_SORT_KEYS=False,
)
```

> **Rule:** configuration keys must be **UPPERCASE**. Flask ignores lowercase keys when loading from objects and files.

### Config classes

A cleaner approach is grouping settings into classes in a `config.py` file. Subclasses inherit and override:

```python
# config.py
import os


class Config:
    """Settings shared by every environment."""
    SECRET_KEY = os.environ.get("SECRET_KEY")
    SQLALCHEMY_DATABASE_URI = os.environ.get("DATABASE_URL", "sqlite:///app.db")
    SQLALCHEMY_TRACK_MODIFICATIONS = False


class DevelopmentConfig(Config):
    DEBUG = True
    SECRET_KEY = os.environ.get("SECRET_KEY", "dev-only-key")


class TestingConfig(Config):
    TESTING = True
    SECRET_KEY = "test-key"
    SQLALCHEMY_DATABASE_URI = "sqlite:///:memory:"
    WTF_CSRF_ENABLED = False


class ProductionConfig(Config):
    DEBUG = False
```

### Loading a config class

```python
app = Flask(__name__)
app.config.from_object("config.DevelopmentConfig")
```

`from_object` accepts either an import string (as shown) or the class itself.

### Useful built-in Flask settings

| Key | Purpose |
|---|---|
| `SECRET_KEY` | Signs sessions and CSRF tokens. Must be secret and random. |
| `DEBUG` | Enables debug mode. |
| `TESTING` | Makes exceptions propagate to the test client. |
| `MAX_CONTENT_LENGTH` | Maximum size of an incoming request body in bytes. |
| `SESSION_COOKIE_SECURE` | Send the session cookie only over HTTPS. |
| `PERMANENT_SESSION_LIFETIME` | How long a permanent session lasts. |
| `SERVER_NAME` | Domain used when building URLs outside a request. |

### Reading configuration

```python
from flask import current_app

secret_ok = bool(current_app.config["SECRET_KEY"])
limit = current_app.config.get("MAX_CONTENT_LENGTH", None)
```

---

## 7.2 Environment Variables

**Environment variables** are values set outside your code, in the operating system or hosting platform. They are the standard way to keep **secrets** (API keys, database passwords) out of your source code.

### Setting variables in a terminal

| Platform | Command |
|---|---|
| macOS / Linux | `export SECRET_KEY="a-long-random-string"` |
| Windows (PowerShell) | `$env:SECRET_KEY = "a-long-random-string"` |
| Windows (cmd) | `set SECRET_KEY=a-long-random-string` |

### Reading them in Python

```python
import os

secret = os.environ.get("SECRET_KEY")          # None if missing
db_url = os.environ.get("DATABASE_URL", "sqlite:///app.db")   # default fallback
required = os.environ["MUST_EXIST"]            # raises KeyError if missing
```

Use `os.environ["NAME"]` for settings that are **required**, so the app fails loudly at startup rather than running insecurely.

### Using a `.env` file

Typing variables every session is tedious. The `python-dotenv` package lets you store them in a `.env` file:

```bash
pip install python-dotenv
```

`.env`:

```
SECRET_KEY=3f1a9c0d5e7b...long-random-hex...
DATABASE_URL=sqlite:///app.db
FLASK_DEBUG=1
```

When `python-dotenv` is installed, the `flask` command **automatically loads** `.env` (and `.flaskenv`) from the current folder before running.

- Put secrets in `.env`.
- Put public, non-secret Flask CLI settings in `.flaskenv`, for example `FLASK_APP=myapp`.

> **Always add `.env` to `.gitignore`.** Committing it leaks your secrets.

### Loading environment variables into `app.config`

Flask can import every variable starting with a given prefix:

```python
app.config.from_prefixed_env()   # default prefix is "FLASK"
```

With these variables set:

```
FLASK_SECRET_KEY=abc123
FLASK_MAX_CONTENT_LENGTH=1048576
FLASK_DEBUG=true
```

the config receives `SECRET_KEY`, `MAX_CONTENT_LENGTH`, and `DEBUG`. Values are parsed as JSON where possible, so numbers and booleans become real numbers and booleans. Double underscores create nested keys: `FLASK_MYSETTING__KEY=1` becomes `config["MYSETTING"]["KEY"]`.

### Generating a good secret key

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

---

## 7.3 Development Vs Production

The same code should behave differently depending on where it runs.

| Setting | Development | Production |
|---|---|---|
| `DEBUG` | `True` | **`False`** |
| `SECRET_KEY` | Any local value | Long, random, private |
| Database | SQLite file | PostgreSQL / MySQL |
| `SESSION_COOKIE_SECURE` | `False` (plain HTTP) | **`True`** (HTTPS) |
| Server | `flask run` | Gunicorn / Waitress behind Nginx |
| Logging | Verbose to console | Structured, to files or a service |
| Error pages | Interactive debugger | Friendly custom pages |

### Choosing a config by environment

Use an environment variable to pick which class to load:

```python
# myapp/__init__.py
import os
from flask import Flask

CONFIGS = {
    "development": "config.DevelopmentConfig",
    "testing": "config.TestingConfig",
    "production": "config.ProductionConfig",
}


def create_app(config_name=None):
    config_name = config_name or os.environ.get("APP_ENV", "development")
    app = Flask(__name__)
    app.config.from_object(CONFIGS[config_name])
    return app
```

Run in production with:

```bash
APP_ENV=production gunicorn "myapp:create_app()"
```

### Failing fast in production

Add a safety check so the app refuses to start with an insecure setup:

```python
class ProductionConfig(Config):
    DEBUG = False
    SESSION_COOKIE_SECURE = True
    SESSION_COOKIE_HTTPONLY = True
    SESSION_COOKIE_SAMESITE = "Lax"


def create_app(config_name=None):
    ...
    if config_name == "production" and not app.config.get("SECRET_KEY"):
        raise RuntimeError("SECRET_KEY must be set in production")
    return app
```

### Debug mode details

- Run with `flask --app myapp run --debug` locally, or set `FLASK_DEBUG=1`.
- Debug mode reloads code on change and shows the interactive debugger.
- **Never** enable it on a server the internet can reach (see Lesson 9).

### Checking the current mode

```python
if app.debug:
    print("Running in debug mode")
if app.testing:
    print("Running tests")
```

---

## 7.4 Instance Folders

The **instance folder** is a special folder for files that are specific to a single deployment and should **not** be committed to version control. Typical contents: a local configuration file with secrets, a SQLite database, or uploaded files.

### Where it lives

By default Flask looks for an `instance` folder next to your application package:

```
flask-project/
├── myapp/
│   └── __init__.py
├── instance/
│   ├── config.py
│   └── app.db
└── wsgi.py
```

Add `instance/` to `.gitignore`.

### Enabling instance-relative config

```python
import os
from flask import Flask


def create_app():
    app = Flask(__name__, instance_relative_config=True)

    # 1. Default settings shipped with the code
    app.config.from_object("config.Config")

    # 2. Per-deployment overrides from instance/config.py (ignored if missing)
    app.config.from_pyfile("config.py", silent=True)

    # 3. Make sure the instance folder exists
    os.makedirs(app.instance_path, exist_ok=True)

    return app
```

- `instance_relative_config=True` makes relative paths in `from_pyfile` resolve to the instance folder.
- `silent=True` means a missing file does not raise an error.
- `app.instance_path` gives the absolute path of the folder.

### Example instance config

`instance/config.py`:

```python
SECRET_KEY = "a-long-random-production-secret"
SQLALCHEMY_DATABASE_URI = "postgresql://user:password@localhost/mydb"
```

Later files override earlier ones, so values here win over `config.Config`.

### Other ways to load config

| Method | Reads from |
|---|---|
| `from_object(obj)` | A class, module, or import string |
| `from_pyfile("file.py")` | A Python file of UPPERCASE assignments |
| `from_envvar("MYAPP_SETTINGS")` | A file whose path is stored in an environment variable |
| `from_prefixed_env()` | Environment variables with a prefix |
| `from_file("config.json", load=json.load)` | A JSON or TOML file |
| `from_mapping(KEY=value)` | Keyword arguments or a dictionary |

### Recommended loading order

```python
app.config.from_object("config.Config")        # defaults
app.config.from_pyfile("config.py", silent=True)  # instance overrides
app.config.from_prefixed_env()                 # environment wins last
```

This gives a clear priority: **environment variables > instance file > defaults**.

### Flask-SQLAlchemy and the instance folder

With Flask-SQLAlchemy 3.x, a relative SQLite URI such as `sqlite:///app.db` is created **inside the instance folder** automatically.

---

[Previous](./[6]-Application-Factory-And-Blueprints.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[8]-Sessions-And-Cookies.md)
