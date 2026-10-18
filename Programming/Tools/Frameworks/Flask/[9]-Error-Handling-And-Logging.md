[Previous](./[8]-Sessions-And-Cookies.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[10]-Databases-With-SQLAlchemy.md)

*Building Applications*

# Lesson 9 - Error Handling And Logging

---

## 9.1 Custom Error Pages

Things go wrong: users mistype URLs, requests contain bad data, and bugs happen. By default, Flask shows plain, unstyled error pages. A polished app shows **friendly, consistent** pages instead.

### Common HTTP errors

| Code | Meaning | Typical cause |
|---|---|---|
| 400 | Bad Request | Malformed or invalid input |
| 401 | Unauthorized | Login required |
| 403 | Forbidden | Logged in but not allowed |
| 404 | Not Found | URL or resource does not exist |
| 405 | Method Not Allowed | Wrong HTTP method |
| 413 | Payload Too Large | Upload exceeded `MAX_CONTENT_LENGTH` |
| 500 | Internal Server Error | An unhandled exception in your code |

### Raising errors on purpose

Use `abort()` to trigger an error from within a view:

```python
from flask import abort

POSTS = {1: "First post", 2: "Second post"}


@app.route("/posts/<int:post_id>")
def show_post(post_id):
    post = POSTS.get(post_id)
    if post is None:
        abort(404)
    return post
```

### Creating error templates

Place templates in a folder like `templates/errors/`:

`templates/errors/404.html`:

```html
{% extends "base.html" %}

{% block title %}Page not found{% endblock %}

{% block content %}
  <h1>404 - Page Not Found</h1>
  <p>Sorry, we could not find what you were looking for.</p>
  <p><a href="{{ url_for('home') }}">Return to the home page</a></p>
{% endblock %}
```

`templates/errors/500.html`:

```html
{% extends "base.html" %}

{% block title %}Server error{% endblock %}

{% block content %}
  <h1>500 - Something Went Wrong</h1>
  <p>We have been notified and are working on it. Please try again later.</p>
{% endblock %}
```

### Wiring templates to errors

A template is not shown until you register a handler (see 9.2):

```python
@app.errorhandler(404)
def page_not_found(error):
    return render_template("errors/404.html"), 404
```

Always return the **status code** alongside the page so browsers, search engines, and API clients know what happened.

### Tips for good error pages

- Keep the tone helpful and calm.
- Offer a link back to safe pages (home, search).
- **Never** show stack traces, file paths, or configuration details to users.
- Use your base layout so the site still looks consistent.

---

## 9.2 Error Handlers

An **error handler** is a function Flask calls when a given error occurs, letting you control the response.

### Handling by status code

```python
from flask import Flask, render_template

app = Flask(__name__)


@app.errorhandler(404)
def not_found(error):
    return render_template("errors/404.html"), 404


@app.errorhandler(403)
def forbidden(error):
    return render_template("errors/403.html"), 403


@app.errorhandler(500)
def server_error(error):
    return render_template("errors/500.html"), 500
```

### Registering without a decorator

This style is handy inside an application factory:

```python
def register_error_handlers(app):
    app.register_error_handler(404, not_found)
    app.register_error_handler(500, server_error)
```

### Handling by exception class

You can handle any Python exception, including your own custom ones:

```python
class PaymentError(Exception):
    def __init__(self, message, status_code=402):
        super().__init__(message)
        self.message = message
        self.status_code = status_code


@app.errorhandler(PaymentError)
def handle_payment_error(error):
    return {"error": error.message}, error.status_code


@app.post("/pay")
def pay():
    raise PaymentError("Card declined")
```

### Handling all HTTP errors

`HTTPException` is the base class of every `abort()` error:

```python
from werkzeug.exceptions import HTTPException


@app.errorhandler(HTTPException)
def handle_http_error(error):
    return {"error": error.name, "message": error.description}, error.code
```

### A catch-all for unexpected exceptions

```python
@app.errorhandler(Exception)
def handle_unexpected(error):
    # Let normal HTTP errors (404, 403, ...) keep their own handling
    if isinstance(error, HTTPException):
        return error
    app.logger.exception("Unhandled exception")
    return render_template("errors/500.html"), 500
```

Always log the exception before returning the generic page so you can diagnose it later.

### Different responses for HTML pages and JSON APIs

```python
from flask import request


@app.errorhandler(404)
def not_found(error):
    if request.path.startswith("/api/"):
        return {"error": "Not found"}, 404
    return render_template("errors/404.html"), 404
```

### How Flask picks a handler

Flask chooses the **most specific** match:

1. A handler for the exact exception class.
2. A handler for a parent class.
3. A handler registered for the HTTP status code.

Blueprint handlers take priority for requests handled by that blueprint. Use `app_errorhandler` on a blueprint to cover the whole application.

### Rolling back on database errors

If you use a database, roll back the session when a 500 error occurs so the next request starts clean:

```python
@app.errorhandler(500)
def server_error(error):
    db.session.rollback()
    return render_template("errors/500.html"), 500
```

---

## 9.3 Logging Setup

**Logging** records what your app is doing so you can monitor and debug it, especially in production where you cannot use the debugger.

### Python logging basics

Python's `logging` module provides **levels** of importance:

| Level | Use for |
|---|---|
| `DEBUG` | Detailed diagnostic information |
| `INFO` | Normal events (user signed in, task finished) |
| `WARNING` | Unexpected but recoverable situations |
| `ERROR` | A failure that stopped an operation |
| `CRITICAL` | A serious failure; the app may not continue |

A logger set to a level records messages at that level **and above**.

### Flask's built-in logger

Every Flask app has `app.logger`:

```python
@app.route("/checkout", methods=["POST"])
def checkout():
    app.logger.info("Checkout started")
    try:
        process_payment()
    except Exception:
        app.logger.exception("Checkout failed")   # includes the full traceback
        abort(500)
    return "Done"
```

Inside blueprints or other modules, use `current_app.logger`, or create a module-level logger:

```python
import logging

logger = logging.getLogger(__name__)
logger.warning("Low disk space")
```

### Configuring logging with dictConfig

Configure logging **before** creating the app so Flask uses it:

```python
from logging.config import dictConfig

dictConfig({
    "version": 1,
    "formatters": {
        "default": {
            "format": "[%(asctime)s] %(levelname)s in %(module)s: %(message)s",
        }
    },
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "stream": "ext://sys.stdout",
            "formatter": "default",
        }
    },
    "root": {"level": "INFO", "handlers": ["console"]},
})

from flask import Flask

app = Flask(__name__)
```

### Logging to a rotating file

A rotating handler prevents a log file from growing without limit:

```python
import logging
import os
from logging.handlers import RotatingFileHandler


def configure_logging(app):
    if app.debug or app.testing:
        return

    os.makedirs("logs", exist_ok=True)
    handler = RotatingFileHandler("logs/app.log", maxBytes=1_000_000, backupCount=5)
    handler.setLevel(logging.INFO)
    handler.setFormatter(logging.Formatter(
        "%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]"
    ))
    app.logger.addHandler(handler)
    app.logger.setLevel(logging.INFO)
    app.logger.info("Application startup")
```

### Logging best practices

- **Log to the console (stdout)** in containers and cloud platforms. The platform collects it for you.
- Use the right level; do not log everything as `ERROR`.
- Use `logger.exception(...)` inside `except` blocks to capture the traceback.
- **Never log secrets**: passwords, tokens, full credit card numbers, or personal data.
- Use lazy formatting: `logger.info("User %s signed in", user_id)`.
- Consider an error-tracking service such as **Sentry** for production alerts.

---

## 9.4 Debug Mode

**Debug mode** is a development aid that makes building apps faster.

### Turning it on

```bash
flask --app app run --debug
```

or set an environment variable:

```bash
export FLASK_DEBUG=1
```

### What it gives you

| Feature | Benefit |
|---|---|
| **Automatic reloader** | The server restarts when you save a file. |
| **Interactive debugger** | When an exception occurs, the browser shows the traceback and lets you inspect variables and run code at any stack frame. |
| **Verbose logging** | `app.logger` outputs at the `DEBUG` level. |

### Reading a debugger traceback

1. The **bottom** frame is where the error occurred.
2. Frames above it show the chain of calls that led there.
3. Click a frame to expand its source code and, if you enter the debugger PIN, open an interactive console.

### Why you must never use it in production

The interactive debugger can **execute arbitrary Python code** on the server. Although it requires a PIN, exposing it to the internet is a severe security risk. Also, the reloader and debug output waste resources.

Make sure production uses:

```python
class ProductionConfig(Config):
    DEBUG = False
    TESTING = False
```

and a production WSGI server instead of `flask run`:

```bash
gunicorn "myapp:create_app()"
```

### Useful debugging techniques

**Print debugging with the logger:**

```python
app.logger.debug("Form data: %s", request.form)
```

**Python's built-in breakpoint:**

```python
@app.route("/inspect")
def inspect():
    value = compute_something()
    breakpoint()          # pauses in the terminal running the server
    return str(value)
```

**Interactive shell with app context:**

```bash
flask --app myapp shell
```

This opens a Python shell with your app context loaded, so you can query models and test functions.

### Debug mode vs the TESTING flag

| Setting | Purpose |
|---|---|
| `DEBUG` | Developer conveniences: reloader, debugger. |
| `TESTING` | Makes exceptions propagate to the test client, and tells extensions they are being tested. |

### Troubleshooting checklist

1. Read the **last line** of the traceback first: it names the error.
2. Check the file and line number it points to.
3. Confirm your template names, endpoint names, and variable names are spelled correctly.
4. Check that your virtual environment is active and dependencies are installed.
5. Reproduce the problem with the simplest possible request.

---

[Previous](./[8]-Sessions-And-Cookies.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[10]-Databases-With-SQLAlchemy.md)
