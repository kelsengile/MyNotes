[Previous](./[14]-Security-Basics.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[16]-The-Flask-Extension-Ecosystem.md)

*Quality And Delivery*

# Lesson 15 - Testing With Pytest

---

## 15.1 Test Client

**Automated tests** are programs that check your app behaves correctly. They catch bugs early, let you refactor with confidence, and act as living documentation. **pytest** is the most popular Python test framework: tests are plain functions using plain `assert` statements.

### Installation

```bash
pip install pytest
```

### Project layout

```
flask-project/
├── myapp/
│   └── __init__.py
├── tests/
│   ├── conftest.py
│   └── test_basic.py
└── requirements.txt
```

pytest discovers files named `test_*.py` and functions named `test_*`.

### The app factory with a test config

Testing is much easier when your app uses the factory pattern (Lesson 6) and accepts optional test settings:

```python
# myapp/__init__.py
from flask import Flask


def create_app(test_config=None):
    app = Flask(__name__)
    app.config.from_mapping(
        SECRET_KEY="dev",
        SQLALCHEMY_DATABASE_URI="sqlite:///app.db",
    )
    if test_config is not None:
        app.config.update(test_config)

    @app.route("/")
    def home():
        return "Hello, Flask!"

    @app.route("/hello/<name>")
    def hello(name):
        return {"message": f"Hello, {name}!"}

    return app
```

### The test client

Flask's **test client** simulates requests to your app without running a real server.

```python
# tests/test_basic.py
from myapp import create_app


def test_home_page():
    app = create_app({"TESTING": True})
    client = app.test_client()

    response = client.get("/")

    assert response.status_code == 200
    assert b"Hello, Flask!" in response.data
```

- `response.status_code` is the HTTP status.
- `response.data` holds the raw **bytes** of the body. Compare with `b"..."` or use `response.get_data(as_text=True)`.
- `response.json` gives parsed JSON for JSON responses.
- `response.headers` and `response.location` (for redirects) are also available.

```python
def test_hello_json():
    client = create_app({"TESTING": True}).test_client()
    response = client.get("/hello/Ada")
    assert response.status_code == 200
    assert response.json == {"message": "Hello, Ada!"}
```

### Sending data

```python
# Form data
client.post("/login", data={"username": "ada", "password": "secret"})

# JSON
client.post("/api/tasks", json={"title": "Write tests"})

# Query string
client.get("/search", query_string={"q": "flask", "page": 2})

# Headers
client.get("/api/me", headers={"Authorization": "Bearer abc123"})
```

### Following redirects

By default the client does **not** follow redirects, which lets you assert on them directly:

```python
def test_redirect_after_login(client):
    response = client.post("/login", data={"username": "ada", "password": "secret"})
    assert response.status_code == 302
    assert response.location.endswith("/dashboard")
```

To follow the redirect and test the final page:

```python
response = client.post("/login", data={...}, follow_redirects=True)
assert b"Welcome" in response.data
```

### Running the tests

```bash
pytest
pytest -v                  # verbose
pytest tests/test_basic.py # one file
pytest -k "login"          # tests with "login" in the name
pytest -x                  # stop at the first failure
```

---

## 15.2 Fixtures

A **fixture** is reusable setup (and teardown) code that pytest passes into tests by name. Fixtures remove repetition and keep each test independent.

### Shared fixtures in conftest.py

pytest automatically loads fixtures from a file called `conftest.py` and makes them available to every test in that folder.

```python
# tests/conftest.py
import pytest
from myapp import create_app


@pytest.fixture
def app():
    app = create_app({
        "TESTING": True,
        "WTF_CSRF_ENABLED": False,
    })
    yield app


@pytest.fixture
def client(app):
    return app.test_client()


@pytest.fixture
def runner(app):
    return app.test_cli_runner()
```

- `yield` splits the fixture: code **before** runs as setup; code **after** (if any) runs as teardown.
- A fixture can use other fixtures by listing them as parameters (`client` uses `app`).

### Using fixtures in tests

Request fixtures simply by naming them as test arguments:

```python
def test_home(client):
    response = client.get("/")
    assert response.status_code == 200


def test_unknown_page(client):
    response = client.get("/does-not-exist")
    assert response.status_code == 404
```

### Fixture scope

By default a fixture runs for **every test** (`scope="function"`). Expensive setup can be shared:

| Scope | Runs once per... |
|---|---|
| `function` (default) | test function |
| `class` | test class |
| `module` | test file |
| `session` | entire test run |

```python
@pytest.fixture(scope="session")
def expensive_resource():
    ...
```

Prefer function scope for anything that holds data, so tests do not influence each other.

### Parametrized tests

Run the same test with several inputs:

```python
import pytest


@pytest.mark.parametrize("path, expected_status", [
    ("/", 200),
    ("/hello/Ada", 200),
    ("/missing", 404),
])
def test_status_codes(client, path, expected_status):
    assert client.get(path).status_code == expected_status
```

### A logged-in client helper

```python
class AuthActions:
    def __init__(self, client):
        self._client = client

    def login(self, username="ada", password="secret"):
        return self._client.post("/auth/login", data={"username": username, "password": password})

    def logout(self):
        return self._client.post("/auth/logout")


@pytest.fixture
def auth(client):
    return AuthActions(client)
```

```python
def test_dashboard_requires_login(client):
    response = client.get("/dashboard")
    assert response.status_code == 302         # redirected to login


def test_dashboard_when_logged_in(client, auth):
    auth.login()
    response = client.get("/dashboard")
    assert response.status_code == 200
```

### Inspecting the session

```python
def test_session_value(client):
    with client.session_transaction() as sess:
        sess["user_id"] = 1                    # set session data before the request
    response = client.get("/dashboard")
    assert response.status_code == 200
```

### Testing the CLI

```python
def test_cli_command(runner):
    result = runner.invoke(args=["seed-db"])
    assert "Database seeded" in result.output
```

### Good test habits

- One logical idea per test; descriptive names (`test_login_fails_with_wrong_password`).
- Arrange, Act, Assert: set up, perform the action, check the result.
- Tests must be **independent** and runnable in any order.
- Test behavior (responses and results), not internal implementation details.

---

## 15.3 Testing Databases

Tests must **never touch your real database**. Use a separate, disposable one.

### Use an in-memory SQLite database

```python
# tests/conftest.py
import pytest
from myapp import create_app
from myapp.extensions import db as _db


@pytest.fixture
def app():
    app = create_app({
        "TESTING": True,
        "SQLALCHEMY_DATABASE_URI": "sqlite:///:memory:",
        "WTF_CSRF_ENABLED": False,
    })

    with app.app_context():
        _db.create_all()
        yield app
        _db.session.remove()
        _db.drop_all()


@pytest.fixture
def client(app):
    return app.test_client()


@pytest.fixture
def db(app):
    return _db
```

- A fresh set of tables is created **before** each test and dropped **after**, so tests start from a clean state.
- `sqlite:///:memory:` keeps data in memory, so tests are fast and leave no files behind.
- Flask-SQLAlchemy 3.x automatically configures in-memory SQLite so the same database is shared within the test.

### Creating test data with fixtures

```python
from myapp.models import User


@pytest.fixture
def user(db):
    user = User(username="ada", email="ada@example.com")
    user.set_password("secret")
    db.session.add(user)
    db.session.commit()
    return user
```

### Testing a model

```python
def test_password_hashing(user):
    assert user.password_hash != "secret"
    assert user.check_password("secret")
    assert not user.check_password("wrong")


def test_user_is_saved(db, user):
    found = db.session.scalar(db.select(User).where(User.username == "ada"))
    assert found is not None
    assert found.email == "ada@example.com"
```

### Testing a view that writes to the database

```python
from myapp.models import Task


def test_create_task(client, db):
    response = client.post("/api/tasks", json={"title": "Write tests"})

    assert response.status_code == 201
    assert response.json["title"] == "Write tests"

    task = db.session.scalar(db.select(Task).where(Task.title == "Write tests"))
    assert task is not None


def test_create_task_requires_title(client):
    response = client.post("/api/tasks", json={})
    assert response.status_code == 400
```

### Testing duplicates and constraints

```python
import pytest
from sqlalchemy.exc import IntegrityError


def test_username_must_be_unique(db, user):
    duplicate = User(username="ada", email="other@example.com")
    db.session.add(duplicate)
    with pytest.raises(IntegrityError):
        db.session.commit()
    db.session.rollback()
```

### Using the real database engine

SQLite behaves differently from PostgreSQL or MySQL in some areas (types, constraints, locking). If your production database is PostgreSQL, consider running a **separate test PostgreSQL database** (for example in Docker) for integration tests, while keeping SQLite for fast unit tests.

### Testing with migrations

At least occasionally, verify that your migrations apply cleanly from scratch:

```bash
flask --app myapp db upgrade
```

This catches problems that `create_all()` hides.

---

## 15.4 Coverage

**Code coverage** measures which lines of your code were executed while the tests ran. It reveals untested parts of your app.

### Installation

```bash
pip install pytest-cov
```

### Running with coverage

```bash
pytest --cov=myapp
```

Sample output:

```
---------- coverage: platform linux, python 3.12 ----------
Name                    Stmts   Miss  Cover
-------------------------------------------
myapp/__init__.py          18      0   100%
myapp/auth/routes.py       42      9    79%
myapp/models.py            25      2    92%
-------------------------------------------
TOTAL                      85     11    87%
```

### Seeing exactly which lines are missed

```bash
pytest --cov=myapp --cov-report=term-missing
```

The `Missing` column lists untested line numbers.

### HTML report

```bash
pytest --cov=myapp --cov-report=html
```

Open `htmlcov/index.html` in a browser for a clickable, line-by-line view, with untested lines highlighted.

### Configuration

Store settings in `pyproject.toml` so you do not retype options:

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--cov=myapp --cov-report=term-missing"

[tool.coverage.run]
branch = true
omit = ["myapp/migrations/*"]

[tool.coverage.report]
fail_under = 80
```

- `branch = true` also measures whether both outcomes of `if` statements were exercised.
- `fail_under = 80` makes the run fail if coverage drops below 80 percent, which is useful in continuous integration.

### Interpreting coverage

| Coverage | What it tells you |
|---|---|
| Low | Large parts of the code are untested. |
| High | Lines were executed, **not necessarily verified**. A test with no assertions can reach 100 percent. |

Coverage shows what you have **not** tested; it does not prove correctness. Aim for meaningful assertions on important behavior (authentication, payments, data changes) rather than chasing 100 percent.

### Continuous integration

Run tests automatically on every push. A minimal GitHub Actions workflow:

```yaml
# .github/workflows/tests.yml
name: tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt pytest pytest-cov
      - run: pytest
```

### Testing checklist

- Test the happy path **and** error cases (bad input, missing data, unauthorized access).
- Keep tests fast so you run them often.
- Use fixtures for setup; keep tests independent.
- Never use production data or credentials in tests.
- Add a test whenever you fix a bug, to prevent regressions.

---

[Previous](./[14]-Security-Basics.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[16]-The-Flask-Extension-Ecosystem.md)
