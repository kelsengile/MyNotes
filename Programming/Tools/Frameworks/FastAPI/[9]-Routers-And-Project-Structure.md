[Previous](./[8]-Forms-Files-And-Headers.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[10]-Middleware-And-Background-Tasks.md)

*Core Features*

# Lesson 9 - Routers And Project Structure

## 9.1 APIRouter

Putting every endpoint in one `main.py` works for small demos, but quickly becomes unmanageable. An **`APIRouter`** is a "mini FastAPI app" that groups related path operations. You define routes on the router, then plug it into the main app.

`routers/items.py`:

```python
from fastapi import APIRouter

router = APIRouter()


@router.get("/items")
def list_items():
    return [{"name": "Keyboard"}, {"name": "Mouse"}]


@router.get("/items/{item_id}")
def get_item(item_id: int):
    return {"item_id": item_id}
```

`main.py`:

```python
from fastapi import FastAPI

from routers import items

app = FastAPI()
app.include_router(items.router)
```

A router supports everything the app does: path and query parameters, request bodies, dependencies, responses, and tags. Using `@router.get(...)` instead of `@app.get(...)` is the only difference.

`include_router` can be called many times, even for routers from other packages. The final OpenAPI documentation merges them into one.

---

## 9.2 Splitting a large app

A realistic example with two feature areas, `users` and `items`, and a shared dependency.

Folder layout:

```
app/
├── __init__.py
├── main.py
├── dependencies.py
└── routers/
    ├── __init__.py
    ├── users.py
    └── items.py
```

`app/dependencies.py`:

```python
from typing import Annotated

from fastapi import Header, HTTPException


async def get_token_header(x_token: Annotated[str, Header()]):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")
```

`app/routers/users.py`:

```python
from fastapi import APIRouter

router = APIRouter()


@router.get("/")
def list_users():
    return [{"username": "ana"}, {"username": "ben"}]


@router.get("/{username}")
def get_user(username: str):
    return {"username": username}
```

`app/routers/items.py`:

```python
from fastapi import APIRouter, Depends, HTTPException

from ..dependencies import get_token_header

router = APIRouter(
    prefix="/items",
    tags=["items"],
    dependencies=[Depends(get_token_header)],
    responses={404: {"description": "Not found"}},
)

fake_items = {"plumbus": {"name": "Plumbus"}, "gun": {"name": "Portal Gun"}}


@router.get("/")
def read_items():
    return fake_items


@router.get("/{item_id}")
def read_item(item_id: str):
    if item_id not in fake_items:
        raise HTTPException(status_code=404, detail="Item not found")
    return fake_items[item_id]
```

`app/main.py`:

```python
from fastapi import FastAPI

from .routers import items, users

app = FastAPI(title="Shop API")

app.include_router(users.router, prefix="/users", tags=["users"])
app.include_router(items.router)


@app.get("/")
def root():
    return {"message": "Welcome"}
```

Run it **from the folder that contains `app/`**:

```bash
fastapi dev app/main.py
```

Notes on imports:

- The `__init__.py` files turn folders into Python **packages**, which allows relative imports like `from ..dependencies import ...`.
- Relative imports (`.` and `..`) only work when the app is run as part of a package; running `python app/routers/items.py` directly will fail.

---

## 9.3 Prefixes and tags

`prefix` and `tags` keep paths DRY and make the docs organized.

You can set them in two places, either on the router or on `include_router`:

```python
# On the router itself
router = APIRouter(prefix="/items", tags=["items"])

# Or when including it
app.include_router(users.router, prefix="/users", tags=["users"])
```

With `prefix="/items"`, a route declared as `@router.get("/{item_id}")` becomes `/items/{item_id}`.

Rules and tips:

- A prefix must start with `/` and must **not** end with `/`.
- `tags` group endpoints into collapsible sections in Swagger UI.
- Setting values in `include_router` is handy when you reuse the same router under different prefixes, such as API versions:

```python
app.include_router(items.router, prefix="/api/v1")
app.include_router(items_v2.router, prefix="/api/v2")
```

Other options accepted by both `APIRouter` and `include_router`:

| Option | Purpose |
|---|---|
| `dependencies` | Dependencies for every route in the router |
| `responses` | Extra documented responses |
| `deprecated` | Mark all routes as deprecated |
| `include_in_schema` | Hide routes from the docs (`False`) |
| `default_response_class` | Default response class for the router |

Routers can also include other routers, which is useful for nesting:

```python
v1 = APIRouter(prefix="/api/v1")
v1.include_router(users.router, prefix="/users")
v1.include_router(items.router)

app.include_router(v1)
```

Finish all `include_router` calls on a router **before** including that router in its parent, because FastAPI copies the routes at the moment of inclusion.

---

## 9.4 Suggested layouts

FastAPI does not force a structure. These two layouts are widely used.

**Layout A: by file type** (good for small and medium projects)

```
app/
├── main.py
├── config.py
├── dependencies.py
├── database.py
├── models/        # database models
│   ├── user.py
│   └── item.py
├── schemas/       # request/response models
│   ├── user.py
│   └── item.py
├── routers/
│   ├── users.py
│   └── items.py
└── services/      # business logic
    ├── user_service.py
    └── item_service.py
tests/
├── conftest.py
└── test_users.py
```

**Layout B: by feature / domain** (scales better for large teams)

```
app/
├── main.py
├── config.py
├── database.py
├── users/
│   ├── router.py
│   ├── schemas.py
│   ├── models.py
│   ├── service.py
│   └── dependencies.py
└── items/
    ├── router.py
    ├── schemas.py
    ├── models.py
    └── service.py
tests/
```

Choosing between them:

| Layout | Strength | Weakness |
|---|---|---|
| By type | Simple and familiar | Related code is spread across folders |
| By feature | Everything about one feature lives together | Needs discipline for shared code |

General guidelines for any layout:

1. **Keep routers thin.** Routers translate HTTP into function calls; business rules live in services.
2. **Separate schemas from database models.** The shape of an API payload is not always the shape of a table.
3. **Centralize configuration** in one module (see Lesson 18).
4. **Mirror your app structure in `tests/`.**
5. **Avoid circular imports** by letting routers import from services and models, never the other way around.
6. Start simple. Splitting into packages early is fine, but do not over-engineer folders you do not yet need.

---

[Previous](./[8]-Forms-Files-And-Headers.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[10]-Middleware-And-Background-Tasks.md)
