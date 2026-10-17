[Previous](./[4]-Request-Bodies-With-Pydantic.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[6]-Dependency-Injection.md)

*Basics*

# Lesson 5 - Responses And Status Codes

## 5.1 response_model

By default FastAPI converts whatever you return into JSON. With a **response model** you control exactly what leaves your API: the output is validated, filtered, and documented.

The most common reason is to **hide sensitive fields**. Here a user is created with a password, but the response must never include it:

```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

app = FastAPI()


class UserIn(BaseModel):
    username: str
    email: EmailStr
    password: str


class UserOut(BaseModel):
    username: str
    email: EmailStr


@app.post("/users", response_model=UserOut)
def create_user(user: UserIn):
    # In a real app you would hash the password and save the user
    return user
```

Even though the function returns the full `UserIn` object (with the password), FastAPI filters it through `UserOut`, so the client only sees `username` and `email`.

You can also declare the model as the **return type annotation**. This is the modern, preferred style because editors and type checkers understand it too:

```python
@app.post("/users")
def create_user(user: UserIn) -> UserOut:
    return user
```

> When the returned object is not literally a `UserOut`, a strict type checker may complain. In that case use `response_model=UserOut` in the decorator instead. If both are present, `response_model` wins.

Useful `response_model` options:

| Option | Effect |
|---|---|
| `response_model_exclude_unset=True` | Omit fields that were never set (ignore defaults) |
| `response_model_exclude_defaults=True` | Omit fields equal to their default value |
| `response_model_exclude_none=True` | Omit fields whose value is `None` |
| `response_model_include={"name"}` | Only include these fields |
| `response_model_exclude={"password"}` | Exclude these fields |

The `include` / `exclude` options are quick shortcuts, but separate input and output models (as above) are clearer and safer.

Lists work naturally:

```python
@app.get("/users", response_model=list[UserOut])
def list_users():
    return [
        {"username": "ana", "email": "ana@example.com", "password": "secret"},
        {"username": "ben", "email": "ben@example.com", "password": "secret"},
    ]
```

---

## 5.2 Status codes

Every HTTP response has a **status code** that tells the client what happened.

| Range | Meaning | Common codes |
|---|---|---|
| `2xx` | Success | `200 OK`, `201 Created`, `204 No Content` |
| `3xx` | Redirection | `301 Moved Permanently`, `307 Temporary Redirect` |
| `4xx` | Client error | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `422 Unprocessable Entity` |
| `5xx` | Server error | `500 Internal Server Error`, `503 Service Unavailable` |

Set the default success code in the decorator with `status_code`:

```python
from fastapi import FastAPI, status

app = FastAPI()


@app.post("/items", status_code=status.HTTP_201_CREATED)
def create_item(name: str):
    return {"name": name}
```

`status.HTTP_201_CREATED` is just the number `201` with a readable name. Using plain numbers (`status_code=201`) works equally well.

**204 No Content** means "success, and there is nothing to return", which is typical for deletes. FastAPI makes sure no body is sent:

```python
@app.delete("/items/{item_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_item(item_id: int):
    # delete from the database here
    return None
```

To signal an error, raise an `HTTPException` (covered in depth in Lesson 7):

```python
from fastapi import HTTPException

items = {1: "Keyboard"}


@app.get("/items/{item_id}")
def get_item(item_id: int):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item": items[item_id]}
```

Document extra possible responses so they appear in `/docs`:

```python
@app.get(
    "/items/{item_id}",
    responses={404: {"description": "Item not found"}},
)
def get_item(item_id: int): ...
```

---

## 5.3 Response classes

FastAPI returns JSON by default. To return something else, use a **response class**. You can set it for the whole path operation with `response_class=...` (which also updates the docs), or return an instance directly.

| Class | Use for |
|---|---|
| `JSONResponse` | JSON with full control over status and headers |
| `HTMLResponse` | HTML pages |
| `PlainTextResponse` | Plain text |
| `RedirectResponse` | Redirecting to another URL |
| `FileResponse` | Sending a file from disk |
| `StreamingResponse` | Streaming data in chunks (see Lesson 8) |

```python
from fastapi import FastAPI
from fastapi.responses import (
    FileResponse,
    HTMLResponse,
    JSONResponse,
    PlainTextResponse,
    RedirectResponse,
)

app = FastAPI()


@app.get("/page", response_class=HTMLResponse)
def page():
    return "<h1>Hello from FastAPI</h1>"


@app.get("/text", response_class=PlainTextResponse)
def text():
    return "Just plain text"


@app.get("/old-page")
def old_page():
    return RedirectResponse(url="/page")


@app.get("/report")
def report():
    return FileResponse("report.pdf", filename="monthly-report.pdf")


@app.get("/custom")
def custom():
    return JSONResponse(
        content={"message": "created"},
        status_code=201,
        headers={"X-Custom": "yes"},
    )
```

Important: when you **return a `Response` object directly**, FastAPI skips `response_model` validation and sends it as is. That gives you full control, but you are responsible for the content.

Avoid reaching for special JSON classes such as `ORJSONResponse` or `UJSONResponse` by default. Modern FastAPI with Pydantic v2 already serializes quickly when you declare a return type or `response_model`.

---

## 5.4 Headers and cookies

To add headers or cookies **while keeping the normal automatic behavior** (JSON conversion, `response_model`), declare a parameter of type `Response`. FastAPI hands you a temporary response object that it merges into the final one.

```python
from fastapi import FastAPI, Response

app = FastAPI()


@app.get("/headers")
def with_headers(response: Response):
    response.headers["X-Request-Source"] = "fastapi-course"
    return {"message": "Check the response headers"}
```

Setting and deleting cookies:

```python
@app.post("/login")
def login(response: Response):
    response.set_cookie(
        key="session_id",
        value="abc123",
        max_age=3600,       # seconds
        httponly=True,      # not readable by JavaScript
        secure=True,        # only sent over HTTPS
        samesite="lax",     # helps protect against CSRF
    )
    return {"message": "Logged in"}


@app.post("/logout")
def logout(response: Response):
    response.delete_cookie("session_id")
    return {"message": "Logged out"}
```

Changing the status code dynamically also works with this parameter:

```python
@app.put("/things/{name}")
def upsert(name: str, response: Response):
    existing = {"a"}
    if name not in existing:
        response.status_code = 201
    return {"name": name}
```

If you build a `JSONResponse` yourself, set headers and cookies on that object instead:

```python
from fastapi.responses import JSONResponse


@app.get("/direct")
def direct():
    response = JSONResponse(content={"ok": True})
    response.headers["X-Direct"] = "true"
    response.set_cookie("theme", "dark")
    return response
```

Reading headers and cookies **sent by the client** is covered in Lesson 8.

---

[Previous](./[4]-Request-Bodies-With-Pydantic.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[6]-Dependency-Injection.md)
