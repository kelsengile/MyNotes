[Previous](./[1]-What-Is-FastAPI.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[3]-Path-And-Query-Parameters.md)

*Basics*

# Lesson 2 - Python Type Hints And Async

## 2.1 Type hints refresher

Type hints let you declare the expected type of a variable, parameter, or return value. Python itself does **not** enforce them at runtime, but tools do: editors use them for autocompletion, type checkers like `mypy` use them to find bugs, and **FastAPI and Pydantic use them to validate data**.

```python
def greet(name: str, times: int = 1) -> str:
    return (f"Hello, {name}! " * times).strip()
```

Common type hints:

| Hint | Meaning |
|---|---|
| `int`, `float`, `str`, `bool` | Basic types |
| `list[int]` | A list of integers |
| `dict[str, float]` | A dictionary with string keys and float values |
| `tuple[int, str]` | A tuple with exactly an int then a str |
| `set[str]` | A set of strings |
| `str \| None` | Either a string or `None` (an optional value) |

```python
def total(prices: list[float]) -> float:
    return sum(prices)


def find_user(user_id: int) -> str | None:
    users = {1: "Ana", 2: "Ben"}
    return users.get(user_id)
```

> `str | None` syntax requires Python 3.10+. On older versions use `Optional[str]` from the `typing` module.

You can also create your own types using classes. In FastAPI this is how request bodies are defined (see Lesson 4):

```python
from pydantic import BaseModel


class User(BaseModel):
    id: int
    name: str
```

---

## 2.2 async def vs def

FastAPI lets you write path operation functions in two ways:

```python
@app.get("/sync")
def read_sync():
    return {"mode": "sync"}


@app.get("/async")
async def read_async():
    return {"mode": "async"}
```

Both work. The difference is **how FastAPI runs them**:

| Function type | How FastAPI runs it |
|---|---|
| `async def` | Directly on the **event loop** (the single thread that handles all async work) |
| `def` | In a separate **thread from a thread pool**, so it does not block the event loop |

The key rule:

- Use `async def` **only if** everything you call inside it can be awaited (for example `await some_async_library()`).
- Use plain `def` if you call regular blocking code (for example a normal database driver, `requests`, `time.sleep`, file reads with the standard library).

If you are unsure, use plain `def`. FastAPI handles it correctly.

The worst mistake is using blocking code inside `async def`:

```python
import time


@app.get("/bad")
async def bad():
    time.sleep(5)  # BLOCKS the entire event loop for 5 seconds
    return {"status": "done"}
```

While `time.sleep(5)` runs, **no other request can be served** by that worker. The fix is either to switch to `def`, or to use the awaitable equivalent:

```python
import asyncio


@app.get("/good")
async def good():
    await asyncio.sleep(5)  # Gives control back to the event loop
    return {"status": "done"}
```

---

## 2.3 Concurrency in practice

**Concurrency** means making progress on multiple tasks by switching between them while one is waiting. A common analogy:

> A cashier takes your order, then instead of staring at the kitchen while your burger cooks, they take the next customer's order. One cashier, many orders in progress.

The "waiting" in web apps is usually **I/O**: waiting for a database, another API, a file, or the network. During that wait an `async` server can handle other requests.

The keyword `await` marks the exact spot where a function says "I'm waiting, you can run something else meanwhile."

```python
import asyncio
import time

from fastapi import FastAPI

app = FastAPI()


async def fetch_price(name: str) -> float:
    await asyncio.sleep(1)  # pretend this is a network call
    return 9.99


@app.get("/prices")
async def prices():
    start = time.perf_counter()

    # Run three "network calls" at the same time
    results = await asyncio.gather(
        fetch_price("a"),
        fetch_price("b"),
        fetch_price("c"),
    )

    return {"results": results, "seconds": round(time.perf_counter() - start, 1)}
```

Awaiting them one by one would take about 3 seconds. With `asyncio.gather` all three run concurrently and the total is about 1 second.

Concurrency vs parallelism:

- **Concurrency** — tasks take turns, great for waiting on I/O.
- **Parallelism** — tasks run literally at the same time on different CPU cores, needed for heavy computation. In production you get parallelism by running multiple worker processes (see Lesson 18).

---

## 2.4 When async helps

Async is a tool, not a requirement. Use this guide:

| Situation | Best choice |
|---|---|
| Calling async libraries (`httpx.AsyncClient`, async database drivers, async Redis) | `async def` |
| Calling blocking libraries (`requests`, classic `psycopg2`, standard SQLAlchemy sessions) | `def` |
| Heavy CPU work (image processing, large calculations, ML inference) | `def`, a background worker, or a separate process |
| Simple endpoints that only return data from memory | Either, the difference is tiny |

**Async mostly helps with I/O-bound workloads with many concurrent requests.** It does not make a single request faster, and it does not make CPU-heavy work faster.

Calling a blocking function from an `async def` path operation safely:

```python
from fastapi import FastAPI
from fastapi.concurrency import run_in_threadpool

app = FastAPI()


def slow_blocking_task() -> str:
    # imagine a blocking library call here
    return "result"


@app.get("/safe")
async def safe():
    result = await run_in_threadpool(slow_blocking_task)
    return {"result": result}
```

Summary checklist:

1. Mixing `def` and `async def` endpoints in one app is perfectly fine.
2. Never call blocking code directly inside `async def`.
3. When in doubt, use `def`.
4. Dependencies and background tasks follow the same `def` vs `async def` rules.

---

[Previous](./[1]-What-Is-FastAPI.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[3]-Path-And-Query-Parameters.md)
