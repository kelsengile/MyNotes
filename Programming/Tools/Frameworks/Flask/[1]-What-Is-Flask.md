[Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[2]-Routing-And-Views.md)

*Basics*

# Lesson 1 - What Is Flask

---

## 1.1 Micro-Framework Meaning

Flask is called a **micro-framework**. "Micro" does **not** mean Flask is tiny, weak, or only for toy projects. It means the **core is small and unopinionated**.

Flask's core gives you:

- **Routing** — mapping URLs to Python functions.
- **Request and response handling** — reading incoming data and sending data back.
- **Templating** — generating HTML with Jinja2.
- **A development server and debugger** — for local work.
- **A test client** — for automated testing.

Flask deliberately does **not** decide these for you:

- Which **database** or ORM to use.
- How to validate **forms**.
- How to handle **authentication**.
- How to structure your **project folders**.

You add those pieces yourself, usually through **extensions** (for example Flask-SQLAlchemy for databases or Flask-Login for authentication).

### Flask vs a "batteries-included" framework

| Concern | Flask | Django (batteries-included) |
|---|---|---|
| Database / ORM | Choose one (e.g. SQLAlchemy) | Built-in ORM |
| Admin interface | Add an extension | Built-in |
| Authentication | Add an extension | Built-in |
| Project structure | Up to you | Enforced conventions |
| Learning curve | Gentle start | Larger up front |

### Why this design matters

- You only install and learn what your project needs.
- You see exactly how each piece fits together, which teaches web fundamentals.
- The trade-off: **you** make more decisions, and you are responsible for keeping them consistent as the project grows.

---

## 1.2 WSGI Explained

Python web frameworks need a standard way to talk to web servers. For synchronous Python web apps, that standard is **WSGI** (Web Server Gateway Interface).

### The idea

A WSGI **application** is simply a Python callable that:

1. Receives a description of the request (the `environ` dictionary).
2. Receives a function (`start_response`) used to send the status and headers.
3. Returns the response body as an iterable of bytes.

Here is the smallest possible WSGI app, with no framework at all:

```python
def application(environ, start_response):
    status = "200 OK"
    headers = [("Content-Type", "text/plain; charset=utf-8")]
    start_response(status, headers)
    return [b"Hello from raw WSGI"]
```

Flask hides this complexity. A Flask application object **is** a WSGI application, which is why any WSGI server can run it.

### The request journey

```
Browser  →  Web server (Nginx)  →  WSGI server (Gunicorn)  →  Flask app  →  Your view function
Browser  ←  Web server (Nginx)  ←  WSGI server (Gunicorn)  ←  Flask app  ←  Return value
```

### The pieces Flask is built on

- **Werkzeug** — a WSGI toolkit. It provides the request and response objects, URL routing, and the development server and debugger.
- **Jinja2** — the template engine that turns templates plus data into HTML.

### Development server vs production server

- `flask run` starts Werkzeug's **development server**. It is convenient but **not designed for production traffic or security**.
- In production you use a real WSGI server such as **Gunicorn** or **Waitress** (covered in Lesson 17).

> **Note:** WSGI handles one request per worker at a time (synchronously). A newer standard, **ASGI**, supports async and WebSockets natively. Flask is a WSGI framework, but it can run `async def` views in a limited way (see Lesson 18).

---

## 1.3 Virtual Environments And Installation

A **virtual environment** is an isolated folder containing its own Python packages. It keeps each project's dependencies separate so that upgrading a library for one project cannot break another.

### Step 1: Check Python

Flask 3.x requires a modern Python 3 (3.9 or newer). Check yours:

```bash
python --version
```

On some systems the command is `python3`.

### Step 2: Create a project folder and virtual environment

```bash
mkdir flask-demo
cd flask-demo
python -m venv .venv
```

This creates a `.venv` folder inside your project.

### Step 3: Activate the environment

| Platform | Command |
|---|---|
| macOS / Linux | `source .venv/bin/activate` |
| Windows (PowerShell) | `.venv\Scripts\Activate.ps1` |
| Windows (cmd) | `.venv\Scripts\activate.bat` |

When active, your terminal prompt usually shows `(.venv)`.

To leave the environment later, run `deactivate`.

### Step 4: Install Flask

```bash
pip install Flask
```

Verify it installed:

```bash
flask --version
```

### Step 5: Record your dependencies

```bash
pip freeze > requirements.txt
```

Anyone can then recreate your environment with:

```bash
pip install -r requirements.txt
```

### Good habits

- Create **one virtual environment per project**.
- Add `.venv/` to your `.gitignore` — never commit it.
- Commit `requirements.txt` so others can reproduce your setup.

---

## 1.4 Your First Flask App

Create a file named `app.py` in your project folder:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def home():
    return "Hello, Flask!"


@app.route("/about")
def about():
    return "<h1>About</h1><p>This is my first Flask app.</p>"
```

### What each part does

| Line | Purpose |
|---|---|
| `from flask import Flask` | Imports the Flask class. |
| `app = Flask(__name__)` | Creates the application. `__name__` tells Flask where to look for templates and static files. |
| `@app.route("/")` | A **decorator** that registers the function below as the handler for the URL `/`. |
| `def home():` | A **view function**. Whatever it returns becomes the HTTP response body. |

### Running the app

From the folder containing `app.py`, with your virtual environment active:

```bash
flask --app app run --debug
```

- `--app app` tells Flask to import the `app` module (the file `app.py`) and find the application inside it.
- `--debug` turns on **debug mode**: the server automatically reloads when you save code, and errors show an interactive debugger in the browser.

Flask prints something like:

```
 * Running on http://127.0.0.1:5000
```

Open `http://127.0.0.1:5000/` in your browser to see `Hello, Flask!`, and `http://127.0.0.1:5000/about` to see the second page.

### Using a different port or host

```bash
flask --app app run --debug --port 8000
```

### Common first-run problems

| Problem | Likely cause and fix |
|---|---|
| `Error: Could not locate a Flask application` | Run the command from the folder containing `app.py`, or pass the correct `--app` name. |
| `flask: command not found` | The virtual environment is not active, or Flask is not installed in it. |
| Port 5000 already in use | Use `--port 8000`, or stop the other program using the port. |
| Changes do not appear | Make sure you used `--debug`, or restart the server manually. |

> **Warning:** Debug mode lets anyone who can reach the server run Python code through the debugger. Use it **only on your own machine**, never on a public server.

---

[Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[2]-Routing-And-Views.md)
