[Previous](./[15]-CORS-And-API-Security.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[17]-OpenAPI-And-Documentation.md)

*Quality And Delivery*

# Lesson 16 - Testing FastAPI Applications

## 16.1 TestClient

FastAPI includes a **`TestClient`** that lets you call your app **without starting a server**. It is built on `httpx` and works like the `requests` library.

```bash
pip install pytest httpx
```

The app under test, `main.py`:

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"1": {"name": "Keyboard"}}


@app.get("/")
def read_root():
    return {"message": "Hello"}


@app.get("/items/{item_id}")
def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return items[item_id]


@app.post("/items", status_code=201)
def create_item(item: dict):
    return item
```

The tests, `test_main.py`:

```python
from fastapi.testclient import TestClient

from main import app

client = TestClient(app)


def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello"}


def test_read_item():
    response = client.get("/items/1")
    assert response.status_code == 200
    assert response.json() == {"name": "Keyboard"}


def test_item_not_found():
    response = client.get("/items/999")
    assert response.status_code == 404
    assert response.json() == {"detail": "Item not found"}


def test_create_item():
    response = client.post("/items", json={"name": "Mouse"})
    assert response.status_code == 201
    assert response.json() == {"name": "Mouse"}
```

Run all tests:

```bash
pytest
```

Useful things to know:

- Test functions are **plain `def`**, even when your endpoints are `async def`. `TestClient` handles the async part.
- Send JSON with `json=...`, form data with `data=...`, files with `files=...`, headers with `headers=...`, query strings with `params=...`.
- Check `response.status_code`, `response.json()`, `response.text`, and `response.headers`.
- Name test files `test_*.py` and functions `test_*` so pytest finds them.

Using `TestClient` as a **context manager** also runs your app's lifespan (startup and shutdown):

```python
def test_with_lifespan():
    with TestClient(app) as client:
        response = client.get("/")
        assert response.status_code == 200
```

---

## 16.2 Pytest fixtures

A **fixture** is reusable setup code that pytest injects into tests by parameter name. Put shared fixtures in `conftest.py`, which pytest loads automatically.

`conftest.py`:

```python
import pytest
from fastapi.testclient import TestClient

from main import app


@pytest.fixture
def client():
    with TestClient(app) as c:
        yield c
```

`test_items.py`:

```python
def test_read_root(client):
    response = client.get("/")
    assert response.status_code == 200
```

Fixtures with `yield` run cleanup code afterwards, just like dependencies. Fixtures can depend on other fixtures, and you choose how often they run with `scope`:

| Scope | Runs |
|---|---|
| `function` (default) | Once per test |
| `module` | Once per test file |
| `session` | Once for the whole test run |

A fixture providing an authenticated client:

```python
@pytest.fixture
def auth_client(client):
    response = client.post("/token", data={"username": "ana", "password": "ana-password"})
    token = response.json()["access_token"]
    client.headers.update({"Authorization": f"Bearer {token}"})
    return client
```

Parametrized tests avoid repeating yourself:

```python
import pytest


@pytest.mark.parametrize(
    "item_id, expected_status",
    [("1", 200), ("999", 404), ("abc", 404)],
)
def test_item_statuses(client, item_id, expected_status):
    response = client.get(f"/items/{item_id}")
    assert response.status_code == expected_status
```

Good test habits:

1. Each test should be **independent**. Do not rely on another test having run first.
2. Test the **happy path** and the **failure cases** (missing data, invalid input, unauthorized).
3. Use clear names that describe behavior, such as `test_create_item_rejects_negative_price`.
4. Keep tests fast so you run them often.

---

## 16.3 Overriding dependencies

Dependency injection makes testing powerful: you can **replace any dependency** with a test version using `app.dependency_overrides`. This is how you avoid hitting real databases, external APIs, or real authentication.

**Example: replace authentication.**

```python
from typing import Annotated

from fastapi import Depends, FastAPI

app = FastAPI()


async def get_current_user():
    # real implementation would validate a token
    raise NotImplementedError


@app.get("/me")
def read_me(user: Annotated[dict, Depends(get_current_user)]):
    return user
```

```python
from fastapi.testclient import TestClient

from main import app, get_current_user


def fake_user():
    return {"username": "test-user"}


def test_me():
    app.dependency_overrides[get_current_user] = fake_user
    try:
        client = TestClient(app)
        response = client.get("/me")
        assert response.json() == {"username": "test-user"}
    finally:
        app.dependency_overrides.clear()    # always clean up
```

The key is the **original dependency function** (`get_current_user`); the value is the replacement. The replacement can itself declare parameters and sub-dependencies.

**Example: a test database with SQLModel** using an in-memory SQLite database:

```python
import pytest
from fastapi.testclient import TestClient
from sqlmodel import Session, SQLModel, create_engine
from sqlmodel.pool import StaticPool

from main import app, get_session


@pytest.fixture(name="session")
def session_fixture():
    engine = create_engine(
        "sqlite://",
        connect_args={"check_same_thread": False},
        poolclass=StaticPool,      # keeps the single in-memory DB alive across connections
    )
    SQLModel.metadata.create_all(engine)
    with Session(engine) as session:
        yield session


@pytest.fixture(name="client")
def client_fixture(session: Session):
    def get_session_override():
        return session

    app.dependency_overrides[get_session] = get_session_override
    client = TestClient(app)
    yield client
    app.dependency_overrides.clear()


def test_create_hero(client: TestClient):
    response = client.post("/heroes", json={"name": "Deadpond", "secret_name": "Dive Wilson"})
    data = response.json()

    assert response.status_code == 201
    assert data["name"] == "Deadpond"
    assert data["id"] is not None
```

Every test gets a brand-new empty database, so tests never affect each other and your real data stays untouched.

Other things worth replacing: email senders, payment clients, HTTP clients calling third-party APIs, and the current time. For code that is not a dependency, use `unittest.mock.patch` or pytest's `monkeypatch`.

---

## 16.4 Async tests

Most tests work fine with the synchronous `TestClient`. You need **async tests** when the test itself must `await` something, such as querying an async database directly or using async fixtures.

Install the helpers:

```bash
pip install pytest-asyncio httpx
```

(The `anyio` pytest plugin, which is already installed with FastAPI's dependencies, is another option.)

Use `httpx.AsyncClient` with `ASGITransport`:

```python
import pytest
from httpx import ASGITransport, AsyncClient

from main import app


@pytest.mark.asyncio
async def test_root_async():
    transport = ASGITransport(app=app)
    async with AsyncClient(transport=transport, base_url="http://test") as client:
        response = await client.get("/")

    assert response.status_code == 200
    assert response.json() == {"message": "Hello"}
```

To avoid marking every test, set the mode in `pytest.ini`:

```ini
[pytest]
asyncio_mode = auto
```

Now plain `async def test_...` functions are collected automatically.

An async fixture for a reusable client:

```python
import pytest_asyncio


@pytest_asyncio.fixture
async def async_client():
    transport = ASGITransport(app=app)
    async with AsyncClient(transport=transport, base_url="http://test") as client:
        yield client


async def test_items(async_client):
    response = await async_client.get("/items/1")
    assert response.status_code == 200
```

Important caveats:

- `AsyncClient` does **not run the lifespan** events. If your app needs startup logic, trigger it yourself, or use the `asgi-lifespan` package's `LifespanManager`.
- Async database tests need an async engine and async session fixtures (see Lesson 13).
- Do not mix event loops: create async engines inside async fixtures, not at module import time, when tests use their own loop.

Testing strategy summary:

| Level | What it checks | Tool |
|---|---|---|
| Unit | Pure functions, validators, services | Plain pytest |
| API / integration | Endpoints, validation, auth, database | `TestClient` + dependency overrides |
| Async-specific | Code paths needing `await` in the test | `AsyncClient` + pytest-asyncio |

Measure coverage with `pip install pytest-cov` and `pytest --cov=app`, but treat the number as a guide, not a goal.

---

[Previous](./[15]-CORS-And-API-Security.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[17]-OpenAPI-And-Documentation.md)
