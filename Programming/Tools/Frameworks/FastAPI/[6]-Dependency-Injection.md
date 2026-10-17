[Previous](./[5]-Responses-And-Status-Codes.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[7]-Validation-And-Error-Handling.md)

*Core Features*

# Lesson 6 - Dependency Injection

## 6.1 What dependencies are

**Dependency injection** means that instead of your function creating or finding the things it needs, it simply **declares** them and the framework supplies them. In FastAPI, a **dependency** is any callable (usually a function) that your path operation asks for with `Depends()`.

Typical uses:

- Shared logic: pagination parameters, filters, common query options.
- Shared resources: database sessions, HTTP clients, settings.
- Security: reading the current user from a token.
- Business rules: checking permissions before the endpoint runs.

A first example, reusable pagination:

```python
from typing import Annotated

from fastapi import Depends, FastAPI

app = FastAPI()


def pagination(skip: int = 0, limit: int = 10) -> dict:
    return {"skip": skip, "limit": limit}


@app.get("/items")
def list_items(page: Annotated[dict, Depends(pagination)]):
    return {"page": page}


@app.get("/users")
def list_users(page: Annotated[dict, Depends(pagination)]):
    return {"page": page}
```

What happens on `GET /items?skip=5`:

1. FastAPI sees `Depends(pagination)`.
2. It reads `skip` and `limit` from the request, exactly as if they were declared on the endpoint.
3. It calls `pagination(skip=5, limit=10)`.
4. It passes the result into `list_items` as `page`.

The dependency's parameters also appear automatically in `/docs`.

**Classes can be dependencies too**, because a class is callable:

```python
class Pagination:
    def __init__(self, skip: int = 0, limit: int = 10):
        self.skip = skip
        self.limit = limit


@app.get("/products")
def list_products(page: Annotated[Pagination, Depends()]):
    return {"skip": page.skip, "limit": page.limit}
```

`Depends()` with no argument uses the type annotation itself (`Pagination`).

To avoid repeating long annotations, create a reusable **type alias**:

```python
PaginationDep = Annotated[Pagination, Depends()]


@app.get("/orders")
def list_orders(page: PaginationDep):
    return {"skip": page.skip, "limit": page.limit}
```

Dependencies may be `def` or `async def`, and you can mix them freely with either type of path operation.

---

## 6.2 Sub-dependencies

A dependency can itself depend on other dependencies. FastAPI resolves the whole tree for you.

```python
from typing import Annotated

from fastapi import Depends, FastAPI, Header, HTTPException

app = FastAPI()


def get_token(authorization: Annotated[str | None, Header()] = None) -> str:
    if not authorization:
        raise HTTPException(status_code=401, detail="Missing Authorization header")
    return authorization.removeprefix("Bearer ")


def get_current_user(token: Annotated[str, Depends(get_token)]) -> dict:
    if token != "secret-token":
        raise HTTPException(status_code=401, detail="Invalid token")
    return {"username": "ana"}


def get_admin_user(user: Annotated[dict, Depends(get_current_user)]) -> dict:
    if user["username"] != "admin":
        raise HTTPException(status_code=403, detail="Admins only")
    return user


@app.get("/me")
def read_me(user: Annotated[dict, Depends(get_current_user)]):
    return user


@app.get("/admin")
def read_admin(admin: Annotated[dict, Depends(get_admin_user)]):
    return admin
```

The chain for `/admin` is: `get_token` → `get_current_user` → `get_admin_user` → endpoint.

**Caching within a request.** If the same dependency is used several times in the same request (directly or through sub-dependencies), FastAPI calls it **only once** and reuses the result. This is important for things like the current user or a database session.

To force a fresh call every time:

```python
Depends(get_current_user, use_cache=False)
```

Raising an `HTTPException` inside a dependency stops the request immediately, so the endpoint never runs.

---

## 6.3 Dependencies with yield

Some dependencies need **setup and cleanup**: open a database session, then close it afterwards. Use `yield` instead of `return`.

```python
from typing import Annotated, Generator

from fastapi import Depends, FastAPI

app = FastAPI()


class FakeSession:
    def __init__(self):
        print("session opened")

    def close(self):
        print("session closed")


def get_session() -> Generator[FakeSession, None, None]:
    session = FakeSession()
    try:
        yield session          # this value is injected into the endpoint
    finally:
        session.close()        # runs after the response has been sent


SessionDep = Annotated[FakeSession, Depends(get_session)]


@app.get("/data")
def read_data(session: SessionDep):
    return {"message": "using the session"}
```

How it works:

1. Code **before** `yield` runs before the endpoint.
2. The yielded value is passed to the endpoint.
3. Code **after** `yield` (inside `finally`) runs after the endpoint finishes, even if an error occurred.

Always wrap the `yield` in `try ... finally` so cleanup is guaranteed.

Async version for async resources:

```python
from typing import AsyncGenerator


async def get_async_client() -> AsyncGenerator[object, None]:
    client = object()  # imagine an async HTTP client here
    try:
        yield client
    finally:
        pass  # await client.aclose()
```

Notes:

- Raising `HTTPException` after `yield` does not change the response that was already built. Handle expected errors before the `yield`, or inside the endpoint.
- Context managers work well here: `with Session(engine) as session: yield session`.

---

## 6.4 Global and router-level dependencies

Sometimes a dependency should run for an endpoint **without its return value being used**, for example checking an API key. Use the `dependencies` parameter.

**On one path operation:**

```python
from fastapi import Depends, FastAPI, Header, HTTPException


def verify_api_key(x_api_key: str = Header()):
    if x_api_key != "my-key":
        raise HTTPException(status_code=403, detail="Invalid API key")


@app.get("/protected", dependencies=[Depends(verify_api_key)])
def protected():
    return {"message": "Welcome"}
```

**On a whole router** (see Lesson 9 for routers):

```python
from fastapi import APIRouter

router = APIRouter(
    prefix="/admin",
    dependencies=[Depends(verify_api_key)],
)


@router.get("/stats")
def stats():
    return {"users": 42}
```

**On the entire application:**

```python
app = FastAPI(dependencies=[Depends(verify_api_key)])
```

Every endpoint in the app now requires the key. Order of execution is: app-level dependencies, then router-level, then decorator-level, then the endpoint's own parameters.

Choosing the right level:

| Level | Best for |
|---|---|
| Parameter on the endpoint | You need the returned value (user, session) |
| `dependencies=[...]` on the decorator | Checks for a single endpoint |
| `dependencies=[...]` on a router | A group of related endpoints |
| `dependencies=[...]` on `FastAPI(...)` | Rules for the entire API |

Dependency injection also makes testing easy: during tests you can replace any dependency with a fake one using `app.dependency_overrides` (see Lesson 16).

---

[Previous](./[5]-Responses-And-Status-Codes.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[7]-Validation-And-Error-Handling.md)
