[Previous](./[2]-Python-Type-Hints-And-Async.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[4]-Request-Bodies-With-Pydantic.md)

*Basics*

# Lesson 3 - Path And Query Parameters

## 3.1 Path parameters

A **path parameter** is a variable part of the URL, declared with curly braces. FastAPI passes it to your function as an argument with the same name.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

- `GET /users/5` returns `{"user_id": 5}` (converted to an `int`).
- `GET /users/abc` returns a `422` error explaining that `user_id` must be an integer.

**Order matters.** Routes are matched from top to bottom, so fixed paths must be declared before parameterized ones:

```python
@app.get("/users/me")
def get_me():
    return {"user": "current user"}


@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

If the order were reversed, `/users/me` would match `{user_id}` and fail validation.

**Paths that contain slashes.** Use the `:path` converter:

```python
@app.get("/files/{file_path:path}")
def read_file(file_path: str):
    return {"file_path": file_path}
```

`GET /files/home/docs/a.txt` gives `file_path = "home/docs/a.txt"`.

---

## 3.2 Query parameters

Any function parameter that is **not** part of the path is treated as a **query parameter**, the `?key=value&key2=value2` part of a URL.

```python
fake_items = [{"name": "Foo"}, {"name": "Bar"}, {"name": "Baz"}]


@app.get("/items")
def list_items(skip: int = 0, limit: int = 10):
    return fake_items[skip : skip + limit]
```

`GET /items?skip=1&limit=2` returns the second and third items.

How FastAPI decides what a parameter is:

| Declaration | Result |
|---|---|
| `q: str` (no default) | **Required** query parameter |
| `q: str = "abc"` | Optional, defaults to `"abc"` |
| `q: str \| None = None` | Optional, `None` when missing |

Types are converted automatically. Booleans accept `true`, `True`, `1`, `on`, `yes` and their false equivalents:

```python
@app.get("/products/{product_id}")
def get_product(product_id: int, short: bool = False, q: str | None = None):
    product = {"product_id": product_id}
    if q:
        product["q"] = q
    if not short:
        product["description"] = "A long description of the product."
    return product
```

`GET /products/3?short=yes&q=test` works as expected.

Path and query parameters can be mixed freely, and FastAPI recognizes each by name.

---

## 3.3 Validation constraints

To add rules such as minimum length, number ranges, or regular expressions, use `Query()` and `Path()` together with `Annotated`.

```python
from typing import Annotated

from fastapi import FastAPI, Path, Query

app = FastAPI()


@app.get("/items/{item_id}")
def read_item(
    item_id: Annotated[int, Path(title="Item ID", ge=1, le=1000)],
    q: Annotated[str | None, Query(min_length=3, max_length=50)] = None,
):
    return {"item_id": item_id, "q": q}
```

Numeric constraints (for `Path`, `Query`, and later `Field`):

| Option | Meaning |
|---|---|
| `gt` | Greater than |
| `ge` | Greater than or equal |
| `lt` | Less than |
| `le` | Less than or equal |

String constraints:

| Option | Meaning |
|---|---|
| `min_length` / `max_length` | Length limits |
| `pattern` | Regular expression the value must match |

```python
@app.get("/search")
def search(
    code: Annotated[str, Query(pattern=r"^[A-Z]{3}-\d{3}$")],
):
    return {"code": code}
```

**Multiple values** for one query parameter, such as `?tag=a&tag=b`, use a list:

```python
@app.get("/filter")
def filter_items(tags: Annotated[list[str], Query()] = []):
    return {"tags": tags}
```

Other useful `Query` options:

```python
q: Annotated[
    str | None,
    Query(
        alias="item-query",          # use ?item-query=... instead of ?q=...
        title="Query string",
        description="Text to search for in item names",
        deprecated=True,             # marks it as deprecated in the docs
    ),
] = None
```

When a parameter is required but also uses `Annotated`, simply leave out the default value.

---

## 3.4 Enums and defaults

To restrict a parameter to a fixed set of values, use a Python `Enum`. Inheriting from `str` lets FastAPI document the allowed values and compare them as strings.

```python
from enum import Enum

from fastapi import FastAPI

app = FastAPI()


class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"


@app.get("/models/{model_name}")
def get_model(model_name: ModelName):
    if model_name is ModelName.alexnet:
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    return {"model_name": model_name, "message": "Have some residuals"}
```

- `GET /models/resnet` works.
- `GET /models/other` returns a `422` listing the valid options.
- In `/docs` the parameter appears as a dropdown.

Enums work for query parameters too, and you can combine them with defaults:

```python
class SortOrder(str, Enum):
    asc = "asc"
    desc = "desc"


@app.get("/articles")
def list_articles(
    order: SortOrder = SortOrder.asc,
    page: int = 1,
    per_page: int = 20,
):
    return {"order": order, "page": page, "per_page": per_page}
```

Rules to remember about defaults:

1. A parameter **with** a default is optional; **without** one it is required.
2. Normal Python rules still apply: parameters without defaults must come before parameters with defaults. Using `Annotated` (instead of writing `Query()` or `Path()` as the default value) means you do not need tricks like a leading `*` to get around this rule.
3. A default of `None` combined with `str | None` means "may be missing".
4. Defaults are shown in the interactive docs so API users can see them.

---

[Previous](./[2]-Python-Type-Hints-And-Async.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[4]-Request-Bodies-With-Pydantic.md)
