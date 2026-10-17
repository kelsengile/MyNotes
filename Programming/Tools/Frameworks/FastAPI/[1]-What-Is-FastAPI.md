[Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[2]-Python-Type-Hints-And-Async.md)

*Basics*

# Lesson 1 - What Is FastAPI

## 1.1 Why FastAPI exists

Before FastAPI, Python developers building web APIs usually picked between **Flask** (simple, but validation, serialization, and documentation were manual) and **Django REST Framework** (powerful, but heavy and tied to Django). Neither was designed around modern Python features like type hints or `async`/`await`.

FastAPI was created by Sebastián Ramírez to fix this. Its core idea is: **declare what your data looks like using standard Python type hints, and let the framework do the rest.**

From a single function signature, FastAPI gives you:

- **Validation** — incoming data is checked and converted to the right types.
- **Serialization** — return values are turned into JSON.
- **Documentation** — an interactive API page is generated automatically.
- **Editor support** — autocompletion and type checking work because everything is plain Python.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}
```

In this tiny example, `item_id` is validated as an integer, `q` is an optional query parameter, and the docs know about both. No extra code needed.

---

## 1.2 ASGI, Starlette, and Pydantic

FastAPI is not built from scratch. It sits on top of two libraries:

| Layer | Library | Responsibility |
|---|---|---|
| Web toolkit | **Starlette** | Routing, requests, responses, middleware, WebSockets, background tasks |
| Data layer | **Pydantic** | Data validation, parsing, and JSON Schema generation |
| Your code | **FastAPI** | Glues them together using type hints and adds dependency injection, security utilities, and OpenAPI docs |

**ASGI** (Asynchronous Server Gateway Interface) is the standard interface between Python web servers and Python web applications. It is the asynchronous successor to **WSGI** (used by Flask and classic Django).

- **WSGI** handles one request per worker at a time, synchronously.
- **ASGI** supports `async`/`await`, long-lived connections (WebSockets), and many concurrent requests per worker.

An ASGI application is any callable that a server such as **Uvicorn** can run. Your `app = FastAPI()` object is exactly that.

```
Client  →  Uvicorn (ASGI server)  →  FastAPI app  →  your path operation function
```

---

## 1.3 Installing and running with Uvicorn

Always work inside a virtual environment so your dependencies stay isolated.

```bash
# Create and activate a virtual environment
python -m venv .venv

# macOS / Linux
source .venv/bin/activate

# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

Install FastAPI with the recommended extras (this includes Uvicorn, the `fastapi` command line tool, and other common dependencies):

```bash
pip install "fastapi[standard]"
```

Create a file named `main.py`:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def root():
    return {"message": "Hello, FastAPI!"}
```

Run it in development mode:

```bash
fastapi dev main.py
```

You can also run Uvicorn directly, which is useful to understand what is happening underneath:

```bash
uvicorn main:app --reload
```

Reading `main:app`:

- `main` is the file `main.py` (the module).
- `app` is the object created inside it with `app = FastAPI()`.
- `--reload` restarts the server whenever you save a file. Use it **only in development**.

By default the server runs at `http://127.0.0.1:8000`.

---

## 1.4 Your first API and its docs

Open `http://127.0.0.1:8000/` in your browser and you will see the JSON response:

```json
{"message": "Hello, FastAPI!"}
```

FastAPI also generates documentation for free:

| URL | What it shows |
|---|---|
| `/docs` | **Swagger UI** — interactive, lets you try requests in the browser |
| `/redoc` | **ReDoc** — clean, read-only reference documentation |
| `/openapi.json` | The raw **OpenAPI** schema that powers both pages |

Let's expand the first API into something slightly more realistic:

```python
from fastapi import FastAPI

app = FastAPI()

fake_db = {1: "Keyboard", 2: "Mouse", 3: "Monitor"}


@app.get("/")
def root():
    return {"message": "Welcome to the shop API"}


@app.get("/items/{item_id}")
def get_item(item_id: int):
    return {"item_id": item_id, "name": fake_db.get(item_id, "Not found")}
```

Key terms you will see throughout this Topic:

- **Path operation** — a combination of an HTTP method (`GET`, `POST`, ...) and a path (`/items/{item_id}`).
- **Path operation decorator** — `@app.get(...)`, `@app.post(...)`, etc.
- **Path operation function** — the function under the decorator that handles the request.

Try visiting `/items/2`, then `/items/abc`. The second request returns a clear `422 Unprocessable Entity` error automatically, because `"abc"` cannot be converted to an `int`. That validation came entirely from the `item_id: int` type hint.

Finally, open `/docs`, expand an endpoint, click **Try it out**, and press **Execute**. You are now testing your API without any extra tool.

---

[Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[2]-Python-Type-Hints-And-Async.md)
