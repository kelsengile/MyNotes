[Previous](./[12]-SQL-Databases-With-SQLModel-And-SQLAlchemy.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[14]-Authentication-With-OAuth2-And-JWT.md)

*Data*

# Lesson 13 - Migrations And Async Databases

## 13.1 Alembic migrations

`SQLModel.metadata.create_all()` creates tables that do not exist, but it **never changes existing tables**. When you add a column, rename a field, or create an index on a database that already holds data, you need a **migration**: a versioned script that upgrades (and can downgrade) the database schema.

**Alembic** is the standard migration tool for SQLAlchemy and SQLModel.

```bash
pip install alembic
alembic init migrations
```

This creates:

```
alembic.ini            # main configuration
migrations/
├── env.py             # how Alembic connects and finds your models
├── script.py.mako     # template for new migration files
└── versions/          # your migration scripts live here
```

**Step 1: set the database URL** in `alembic.ini`:

```ini
sqlalchemy.url = sqlite:///database.db
```

(In real projects read the URL from an environment variable inside `env.py` instead of hard-coding it.)

**Step 2: tell Alembic about your models** in `migrations/env.py`:

```python
from sqlmodel import SQLModel

# Import every module that defines table models so they register on the metadata
from app.models import Hero, Team   # noqa: F401

target_metadata = SQLModel.metadata
```

If you use SQLModel, also add `import sqlmodel` to the top of `migrations/script.py.mako`, so generated scripts can reference `sqlmodel.sql.sqltypes.AutoString`.

**Step 3: generate a migration** by comparing your models with the database:

```bash
alembic revision --autogenerate -m "create hero table"
```

Always **open and review** the generated file in `versions/`. Autogenerate is helpful but cannot detect everything (for example column renames look like a drop plus an add).

```python
def upgrade() -> None:
    op.create_table(
        "hero",
        sa.Column("id", sa.Integer(), nullable=False),
        sa.Column("name", sqlmodel.sql.sqltypes.AutoString(), nullable=False),
        sa.PrimaryKeyConstraint("id"),
    )


def downgrade() -> None:
    op.drop_table("hero")
```

**Step 4: apply it:**

```bash
alembic upgrade head       # apply all pending migrations
alembic downgrade -1       # undo the last migration
alembic current            # show the current version
alembic history            # list all migrations
```

Workflow when your models change:

1. Edit the model (for example add `email: str | None = None`).
2. Run `alembic revision --autogenerate -m "add hero email"`.
3. Review the script.
4. Run `alembic upgrade head`.
5. Commit the migration file to version control.

Once you use Alembic, **stop calling `create_all()`** in your app startup; migrations become the single source of truth for your schema. Never edit a migration that has already been applied elsewhere; create a new one instead.

---

## 13.2 Async database drivers

Lesson 2 explained that blocking calls inside `async def` hurt performance. A normal database driver blocks while waiting for results. **Async drivers** let the event loop serve other requests during that wait.

| Database | Async driver | SQLAlchemy URL |
|---|---|---|
| SQLite | `aiosqlite` | `sqlite+aiosqlite:///./app.db` |
| PostgreSQL | `asyncpg` | `postgresql+asyncpg://user:pass@host/dbname` |
| MySQL / MariaDB | `aiomysql` or `asyncmy` | `mysql+aiomysql://user:pass@host/dbname` |

```bash
pip install aiosqlite          # for SQLite
pip install asyncpg            # for PostgreSQL
```

**Async SQLModel example:**

```python
from collections.abc import AsyncGenerator
from contextlib import asynccontextmanager
from typing import Annotated

from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.ext.asyncio import AsyncEngine, async_sessionmaker, create_async_engine
from sqlmodel import Field, SQLModel, select
from sqlmodel.ext.asyncio.session import AsyncSession


class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str = Field(index=True)


engine: AsyncEngine = create_async_engine("sqlite+aiosqlite:///./async.db", echo=False)
async_session_maker = async_sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)


@asynccontextmanager
async def lifespan(app: FastAPI):
    async with engine.begin() as conn:
        await conn.run_sync(SQLModel.metadata.create_all)
    yield
    await engine.dispose()


app = FastAPI(lifespan=lifespan)


async def get_session() -> AsyncGenerator[AsyncSession, None]:
    async with async_session_maker() as session:
        yield session


SessionDep = Annotated[AsyncSession, Depends(get_session)]


@app.post("/heroes", response_model=Hero, status_code=201)
async def create_hero(hero: Hero, session: SessionDep):
    session.add(hero)
    await session.commit()
    await session.refresh(hero)
    return hero


@app.get("/heroes", response_model=list[Hero])
async def list_heroes(session: SessionDep):
    result = await session.exec(select(Hero))
    return result.all()


@app.get("/heroes/{hero_id}", response_model=Hero)
async def get_hero(hero_id: int, session: SessionDep):
    hero = await session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    return hero
```

What changed compared with the sync version:

- `create_async_engine` and an **async session**.
- Every database call is **awaited** (`await session.commit()`, `await session.get(...)`).
- The endpoints use `async def`.
- `expire_on_commit=False` avoids lazy-loading attributes after commit, which would require another `await` and fail in async code.

**Lazy loading does not work in async.** Accessing `hero.team` on an object that has not loaded it raises an error. Load related data explicitly:

```python
from sqlalchemy.orm import selectinload

result = await session.exec(select(Hero).options(selectinload(Hero.team)))
```

If you use plain SQLAlchemy instead of SQLModel, import `AsyncSession` from `sqlalchemy.ext.asyncio` and use `await session.execute(select(...))` followed by `.scalars().all()`.

Use async drivers when you have many concurrent requests that spend time waiting on the database. For small apps or CPU-heavy work, the synchronous version in plain `def` endpoints is perfectly fine.

---

## 13.3 Connection pooling

Opening a database connection is slow. A **connection pool** keeps a set of open connections and lends them out to requests, then takes them back.

SQLAlchemy creates a pool automatically inside the engine. Tune it with parameters (not applicable to SQLite in-memory/file defaults, so these apply mainly to PostgreSQL and MySQL):

```python
from sqlalchemy.ext.asyncio import create_async_engine

engine = create_async_engine(
    "postgresql+asyncpg://user:password@localhost/mydb",
    pool_size=10,         # connections kept open permanently
    max_overflow=20,      # extra temporary connections allowed under load
    pool_timeout=30,      # seconds to wait for a free connection before erroring
    pool_recycle=1800,    # replace connections older than 30 minutes
    pool_pre_ping=True,   # test a connection before using it
)
```

| Setting | Why it matters |
|---|---|
| `pool_size` | Baseline number of ready connections |
| `max_overflow` | Burst capacity; maximum total = `pool_size + max_overflow` |
| `pool_timeout` | How long a request waits when the pool is exhausted |
| `pool_recycle` | Avoids using connections the server or a firewall has silently closed |
| `pool_pre_ping` | Detects dead connections and replaces them transparently |

Sizing guidance:

1. **Total connections = workers × (pool_size + max_overflow)**. If you run 4 worker processes with a pool of 10 + 20, the database may receive up to 120 connections. Databases such as PostgreSQL have a default limit (often 100), so check it.
2. More connections are not always faster. Past a point the database slows down.
3. For many app instances, put a pooler such as **PgBouncer** between the app and PostgreSQL.
4. Always return connections: use dependencies with `yield` so sessions close even on errors. Leaked sessions exhaust the pool and cause `TimeoutError: QueuePool limit reached` errors.
5. Call `await engine.dispose()` on shutdown (as in the lifespan above).

---

## 13.4 NoSQL options

SQL databases are not the only choice. FastAPI works with any database that has a Python client.

| Type | Example | Typical use |
|---|---|---|
| Document | **MongoDB** | Flexible, JSON-like records with changing structure |
| Key-value / cache | **Redis** | Caching, sessions, rate limiting, queues, pub/sub |
| Wide-column | **Cassandra**, **ScyllaDB** | Very high write volume, time-series style data |
| Search | **Elasticsearch**, **OpenSearch** | Full-text search |

Since FastAPI does not tie you to one tool, you manage connection setup in the **lifespan** and expose clients through **dependencies**.

**Redis example** (async client from the `redis` package):

```python
from contextlib import asynccontextmanager
from typing import Annotated

import redis.asyncio as redis
from fastapi import Depends, FastAPI, Request


@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.redis = redis.from_url("redis://localhost:6379", decode_responses=True)
    yield
    await app.state.redis.aclose()


app = FastAPI(lifespan=lifespan)


def get_redis(request: Request) -> redis.Redis:
    return request.app.state.redis


RedisDep = Annotated[redis.Redis, Depends(get_redis)]


@app.get("/hits")
async def hits(r: RedisDep):
    count = await r.incr("page_hits")
    return {"hits": count}
```

**MongoDB**: use **PyMongo's async API** (the official successor to the older Motor library) or an ODM such as **Beanie** (built on Pydantic, which fits FastAPI well). The pattern is the same: create the client in `lifespan`, close it on shutdown, and inject the database through a dependency.

Choosing SQL or NoSQL:

| Prefer SQL when | Prefer NoSQL when |
|---|---|
| Data is relational (users, orders, items) | Data is document-shaped or schema changes frequently |
| You need transactions across several tables | You need extreme scale for simple access patterns |
| Reporting and joins matter | You need caching or fast ephemeral data (Redis) |

A very common production setup combines them: **PostgreSQL** as the main database plus **Redis** for caching and rate limiting.

---

[Previous](./[12]-SQL-Databases-With-SQLModel-And-SQLAlchemy.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[14]-Authentication-With-OAuth2-And-JWT.md)
