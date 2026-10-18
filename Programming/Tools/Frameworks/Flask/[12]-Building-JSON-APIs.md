[Previous](./[11]-Migrations-With-Flask-Migrate.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[13]-Authentication-With-Flask-Login.md)

*Data*

# Lesson 12 - Building JSON APIs

---

## 12.1 jsonify And request.json

An **API** (Application Programming Interface) lets programs talk to your app. Web and mobile front ends, other services, and scripts typically exchange data as **JSON** (JavaScript Object Notation), a lightweight text format:

```json
{"id": 1, "title": "Learn Flask", "done": false}
```

### Sending JSON

Flask converts dictionaries and lists to JSON automatically when you return them:

```python
from flask import Flask, jsonify, request

app = Flask(__name__)


@app.route("/api/ping")
def ping():
    return {"status": "ok"}
```

`jsonify()` does the same but returns a response object, which is useful for lists built on the fly or when you need to adjust headers:

```python
@app.route("/api/colors")
def colors():
    return jsonify(["red", "green", "blue"])
```

Add a status code with a tuple:

```python
@app.post("/api/items")
def create_item():
    return {"id": 1, "name": "Widget"}, 201
```

### Receiving JSON

The client sends a body with the header `Content-Type: application/json`. Read it with `request.get_json()`:

```python
@app.post("/api/echo")
def echo():
    data = request.get_json(silent=True)
    if data is None:
        return {"error": "Request body must be valid JSON"}, 400
    return {"received": data}
```

- `silent=True` returns `None` instead of raising an error when the body is missing or invalid, letting you send your own message.
- Always **validate** the data before using it (see 12.3).

### Trying your API from the command line

```bash
curl -X POST http://127.0.0.1:5000/api/echo \
  -H "Content-Type: application/json" \
  -d '{"name": "Ada", "age": 36}'
```

Response:

```json
{"received": {"age": 36, "name": "Ada"}}
```

### Types JSON can represent

| JSON | Python |
|---|---|
| object | `dict` |
| array | `list` |
| string | `str` |
| number | `int` / `float` |
| `true` / `false` | `True` / `False` |
| `null` | `None` |

Flask also converts common extras such as `datetime`, `date`, `UUID`, and `Decimal` when serializing responses. For model objects, convert them to dictionaries first (see 12.3).

### Consistent error responses

Decide on one shape for errors and use it everywhere:

```python
from werkzeug.exceptions import HTTPException


@app.errorhandler(HTTPException)
def handle_http_error(error):
    return {"error": {"code": error.code, "message": error.description}}, error.code
```

---

## 12.2 REST Endpoint Design

**REST** is a popular style for designing HTTP APIs. The core idea: URLs identify **resources** (things), and HTTP methods say what to do with them.

### Resource-oriented URLs

Use **nouns** (plural) in URLs, not verbs:

| Action | Method and URL | Typical success status |
|---|---|---|
| List all tasks | `GET /api/tasks` | 200 |
| Get one task | `GET /api/tasks/5` | 200 |
| Create a task | `POST /api/tasks` | 201 |
| Replace a task | `PUT /api/tasks/5` | 200 |
| Update part of a task | `PATCH /api/tasks/5` | 200 |
| Delete a task | `DELETE /api/tasks/5` | 204 |

Avoid URLs like `/api/getTask` or `/api/deleteTask`; the method already expresses the action.

### Status codes for APIs

| Situation | Code |
|---|---|
| Success | 200 |
| Created a resource | 201 |
| Success, nothing to return | 204 |
| Invalid input | 400 (or 422) |
| Not logged in / bad token | 401 |
| Not allowed | 403 |
| Resource missing | 404 |
| Conflict (e.g. duplicate) | 409 |

### A complete CRUD example

This example stores tasks in a dictionary so it runs without a database. (In a real app, replace the dictionary with SQLAlchemy queries from Lesson 10.)

```python
from flask import Flask, request, abort

app = Flask(__name__)

tasks = {}
next_id = 1


@app.get("/api/tasks")
def list_tasks():
    return {"tasks": list(tasks.values())}


@app.get("/api/tasks/<int:task_id>")
def get_task(task_id):
    task = tasks.get(task_id)
    if task is None:
        abort(404, description="Task not found")
    return task


@app.post("/api/tasks")
def create_task():
    global next_id
    data = request.get_json(silent=True) or {}
    title = data.get("title")
    if not title:
        abort(400, description="'title' is required")
    task = {"id": next_id, "title": title, "done": False}
    tasks[next_id] = task
    next_id += 1
    return task, 201


@app.patch("/api/tasks/<int:task_id>")
def update_task(task_id):
    task = tasks.get(task_id)
    if task is None:
        abort(404, description="Task not found")
    data = request.get_json(silent=True) or {}
    if "title" in data:
        task["title"] = data["title"]
    if "done" in data:
        task["done"] = bool(data["done"])
    return task


@app.delete("/api/tasks/<int:task_id>")
def delete_task(task_id):
    if tasks.pop(task_id, None) is None:
        abort(404, description="Task not found")
    return "", 204
```

### Filtering, sorting, and pagination

Use query parameters for options on collections:

```
GET /api/tasks?done=true&sort=title&page=2&per_page=20
```

```python
@app.get("/api/tasks")
def list_tasks():
    page = request.args.get("page", 1, type=int)
    per_page = min(request.args.get("per_page", 20, type=int), 100)  # cap the page size
    items = list(tasks.values())
    start = (page - 1) * per_page
    return {
        "tasks": items[start:start + per_page],
        "page": page,
        "per_page": per_page,
        "total": len(items),
    }
```

### Versioning

Prefix URLs with a version so you can change the API later without breaking existing clients:

```python
from flask import Blueprint

api_v1 = Blueprint("api_v1", __name__, url_prefix="/api/v1")
```

### Design checklist

- Plural nouns for collections; IDs identify single items.
- Correct methods and status codes.
- JSON in and JSON out, with consistent error shapes.
- Never expose secrets or internal details in responses.
- Cap page sizes to protect the server.
- Document the API (for example with OpenAPI/Swagger).

---

## 12.3 Serialization With Marshmallow

**Serialization** converts Python objects into JSON-friendly data. **Deserialization** converts incoming JSON into validated Python data. **Marshmallow** is a popular library that handles both, plus validation.

### Why use it

- Validate incoming data in one place instead of scattered `if` checks.
- Control exactly which fields appear in responses (for example, never expose `password_hash`).
- Produce clear, structured error messages.

### Installation

```bash
pip install marshmallow
```

### Defining a schema

```python
from marshmallow import Schema, fields, validate


class TaskSchema(Schema):
    id = fields.Int(dump_only=True)                       # included in output, ignored on input
    title = fields.Str(required=True, validate=validate.Length(min=1, max=100))
    done = fields.Bool(load_default=False)
```

| Option | Meaning |
|---|---|
| `required=True` | The field must be present when loading. |
| `dump_only=True` | Output only; clients cannot set it. |
| `load_only=True` | Input only (such as a password); never included in output. |
| `load_default=...` | Value used when the field is missing on input. |
| `validate=...` | One or more validators (`Length`, `Range`, `OneOf`, `Email`, custom functions). |

### Loading and validating input

```python
from marshmallow import ValidationError

task_schema = TaskSchema()
tasks_schema = TaskSchema(many=True)


@app.post("/api/tasks")
def create_task():
    global next_id
    json_data = request.get_json(silent=True)
    if json_data is None:
        return {"error": "Invalid JSON"}, 400
    try:
        data = task_schema.load(json_data)
    except ValidationError as err:
        return {"errors": err.messages}, 400

    task = {"id": next_id, **data}
    tasks[next_id] = task
    next_id += 1
    return task_schema.dump(task), 201
```

If the client sends `{"title": ""}`, the response looks like:

```json
{"errors": {"title": ["Length must be between 1 and 100."]}}
```

By default, Marshmallow **rejects unknown fields**, which helps catch typos and unexpected input.

### Dumping output

```python
@app.get("/api/tasks")
def list_tasks():
    return {"tasks": tasks_schema.dump(tasks.values())}
```

### Partial updates for PATCH

```python
@app.patch("/api/tasks/<int:task_id>")
def update_task(task_id):
    task = tasks.get(task_id)
    if task is None:
        abort(404)
    try:
        data = task_schema.load(request.get_json(silent=True) or {}, partial=True)
    except ValidationError as err:
        return {"errors": err.messages}, 400
    task.update(data)
    return task_schema.dump(task)
```

`partial=True` skips the `required` check so clients can send just the fields they want to change.

### Using it with SQLAlchemy models

`dump()` works on objects as well as dictionaries, reading attributes by name:

```python
class UserSchema(Schema):
    id = fields.Int(dump_only=True)
    username = fields.Str(required=True)
    email = fields.Email(required=True)
    password = fields.Str(load_only=True, required=True)   # never returned


@app.get("/api/users/<int:user_id>")
def get_user(user_id):
    user = db.get_or_404(User, user_id)
    return UserSchema().dump(user)
```

> The `flask-marshmallow` and `marshmallow-sqlalchemy` packages can generate schemas automatically from your models, which reduces repetition in larger projects.

---

## 12.4 CORS

### The problem

Browsers enforce the **same-origin policy**: JavaScript running on `https://myfrontend.com` is not allowed to read responses from `https://api.mybackend.com` unless the API explicitly permits it. An *origin* is the combination of scheme, host, and port.

If your front end and API are on different origins (a React app on port 3000 and Flask on port 5000, for example), the browser blocks the calls and shows an error such as:

```
Access to fetch at 'http://localhost:5000/api/tasks' from origin 'http://localhost:3000'
has been blocked by CORS policy
```

**CORS** (Cross-Origin Resource Sharing) is the mechanism that lets a server say which origins are allowed.

### How it works

1. For simple requests, the browser sends the request and checks the response header `Access-Control-Allow-Origin`.
2. For requests that could change data or use custom headers (such as JSON `POST` with `Authorization`), the browser first sends a **preflight** `OPTIONS` request asking what is allowed.
3. The server answers with headers listing allowed origins, methods, and headers.

### Flask-CORS

```bash
pip install flask-cors
```

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)

# Allow a specific front end to call routes under /api/
CORS(app, resources={r"/api/*": {"origins": ["http://localhost:3000", "https://myfrontend.com"]}})
```

### Common options

| Option | Purpose |
|---|---|
| `origins` | Which origins may call the API. |
| `methods` | Allowed HTTP methods. |
| `allow_headers` | Allowed request headers (such as `Authorization`, `Content-Type`). |
| `supports_credentials` | Allow cookies and authentication data to be sent. |
| `max_age` | How long browsers may cache preflight answers. |

### Enabling it on one blueprint or route

```python
from flask_cors import cross_origin

@app.route("/api/public-data")
@cross_origin(origins="https://partner-site.com")
def public_data():
    return {"data": [1, 2, 3]}
```

### Security notes

- **Avoid `origins="*"` for APIs that use cookies or sensitive data.** A wildcard lets any website call your API from a user's browser.
- List only the origins you control.
- If you use `supports_credentials=True`, you must list explicit origins; wildcards are not allowed with credentials.
- CORS is enforced by **browsers only**. It is not authentication. Tools like `curl` ignore it, so always protect endpoints with proper authentication and authorization.
- CORS does not replace CSRF protection when you use cookie-based sessions.

### Alternatives to CORS

- Serve the front end and API from the **same origin**, for example by having Nginx route `/api/` to Flask and everything else to the front end.
- During front-end development, use the dev server's **proxy** feature to forward `/api` calls to Flask.

---

[Previous](./[11]-Migrations-With-Flask-Migrate.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[13]-Authentication-With-Flask-Login.md)
