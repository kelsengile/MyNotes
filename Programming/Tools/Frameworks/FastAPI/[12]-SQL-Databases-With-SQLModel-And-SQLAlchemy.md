[Previous](./[11]-WebSockets.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[13]-Migrations-And-Async-Databases.md)

*Data*

# Lesson 12 - SQL Databases With SQLModel And SQLAlchemy

## 12.1 Models and sessions

FastAPI does not require any specific database tool, but SQL databases are used most often. Two common choices:

| Tool | Description |
|---|---|
| **SQLAlchemy** | The most widely used Python SQL toolkit and ORM. Powerful and flexible |
| **SQLModel** | Created by the author of FastAPI. It is built **on top of SQLAlchemy and Pydantic**, so one class can be both a database table and a validation model |

This lesson uses **SQLModel** for the main examples and shows plain SQLAlchemy at the end.

Install:

```bash
pip install sqlmodel
```

SQLite is a file-based database that needs no server, perfect for learning. The same code works with PostgreSQL or MySQL by changing the connection URL and installing the right driver (for example `psycopg` for PostgreSQL).

Key concepts:

- **Model**: a Python class representing a table. Each attribute is a column.
- **Engine**: the object that manages the connection to the database. Create **one** per application.
- **Session**: a short-lived workspace in which you read and write objects. Create **one per request**.

```python
from sqlmodel import Field, Session, SQLModel, create_engine


class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str = Field(index=True)
    secret_name: str
    age: int | None = Field(default=None, index=True)


sqlite_url = "sqlite:///database.db"
connect_args = {"check_same_thread": False}   # required for SQLite with FastAPI threads
engine = create_engine(sqlite_url, connect_args=connect_args)


def create_db_and_tables():
    SQLModel.metadata.create_all(engine)
```

Explanation:

- `table=True` makes the class a real database table. Without it, it is just a data model.
- `id` is `None` until the database assigns it, so its type is `int | None`.
- `index=True` creates a database index to speed up searches on that column.
- `create_all` creates missing tables. It does **not** alter existing ones (use migrations, Lesson 13).

---

## 12.2 CRUD operations

**CRUD** means **C**reate, **R**ead, **U**pdate, **D**elete. A good practice is to use **separate models** for what the API receives and returns, so clients cannot set the `id` or see internal fields.

```python
from sqlmodel import Field, SQLModel


class HeroBase(SQLModel):
    name: str = Field(index=True)
    age: int | None = Field(default=None, index=True)


class Hero(HeroBase, table=True):          # the database table
    id: int | None = Field(default=None, primary_key=True)
    secret_name: str


class HeroCreate(HeroBase):                # data needed to create
    secret_name: str


class HeroPublic(HeroBase):                # data returned to clients
    id: int


class HeroUpdate(SQLModel):                # every field optional for PATCH
    name: str | None = None
    age: int | None = None
    secret_name: str | None = None
```

The session dependency and app setup:

```python
from contextlib import asynccontextmanager
from typing import Annotated

from fastapi import Depends, FastAPI, HTTPException, Query
from sqlmodel import Session, select


def get_session():
    with Session(engine) as session:
        yield session


SessionDep = Annotated[Session, Depends(get_session)]


@asynccontextmanager
async def lifespan(app: FastAPI):
    create_db_and_tables()
    yield


app = FastAPI(lifespan=lifespan)
```

**Create:**

```python
@app.post("/heroes", response_model=HeroPublic, status_code=201)
def create_hero(hero: HeroCreate, session: SessionDep):
    db_hero = Hero.model_validate(hero)
    session.add(db_hero)
    session.commit()
    session.refresh(db_hero)      # reload to get the generated id
    return db_hero
```

**Read (list with pagination, and one):**

```python
@app.get("/heroes", response_model=list[HeroPublic])
def read_heroes(
    session: SessionDep,
    offset: int = 0,
    limit: Annotated[int, Query(le=100)] = 100,
):
    return session.exec(select(Hero).offset(offset).limit(limit)).all()


@app.get("/heroes/{hero_id}", response_model=HeroPublic)
def read_hero(hero_id: int, session: SessionDep):
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    return hero
```

**Update (partial):**

```python
@app.patch("/heroes/{hero_id}", response_model=HeroPublic)
def update_hero(hero_id: int, data: HeroUpdate, session: SessionDep):
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")

    changes = data.model_dump(exclude_unset=True)   # only what the client sent
    hero.sqlmodel_update(changes)
    session.add(hero)
    session.commit()
    session.refresh(hero)
    return hero
```

**Delete:**

```python
@app.delete("/heroes/{hero_id}", status_code=204)
def delete_hero(hero_id: int, session: SessionDep):
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    session.delete(hero)
    session.commit()
```

Querying with filters:

```python
heroes = session.exec(select(Hero).where(Hero.age >= 18).order_by(Hero.name)).all()
first = session.exec(select(Hero).where(Hero.name == "Deadpond")).first()
```

Remember: changes are not saved until `session.commit()`. If an error occurs, call `session.rollback()` (leaving the `with Session(...)` block without committing discards pending changes).

---

## 12.3 Relationships

Real data is connected. For example, one **Team** has many **Heroes**, and each hero belongs to one team (a **one-to-many** relationship).

```python
from sqlmodel import Field, Relationship, SQLModel


class Team(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str = Field(index=True)
    headquarters: str

    heroes: list["Hero"] = Relationship(back_populates="team")


class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str = Field(index=True)

    team_id: int | None = Field(default=None, foreign_key="team.id")
    team: Team | None = Relationship(back_populates="heroes")
```

Concepts:

| Piece | Meaning |
|---|---|
| `foreign_key="team.id"` | A real database column linking to the `id` of the `team` table (the table name is the lower-cased class name) |
| `Relationship(...)` | A Python-side attribute for navigating between objects, not a column |
| `back_populates` | Connects the two sides so changes stay in sync |

Using them:

```python
with Session(engine) as session:
    team = Team(name="Preventers", headquarters="Sharp Tower")
    hero = Hero(name="Spider-Boy", team=team)   # assigns team_id automatically
    session.add(hero)
    session.commit()

    session.refresh(hero)
    print(hero.team.name)        # Preventers
    print(team.heroes)           # [Hero(name='Spider-Boy', ...)]
```

To **return related data** from the API, create response models that include it:

```python
class HeroPublic(SQLModel):
    id: int
    name: str
    team_id: int | None = None


class TeamPublic(SQLModel):
    id: int
    name: str
    headquarters: str


class HeroPublicWithTeam(HeroPublic):
    team: TeamPublic | None = None


@app.get("/heroes/{hero_id}/with-team", response_model=HeroPublicWithTeam)
def read_hero_with_team(hero_id: int, session: SessionDep):
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    return hero
```

Other relationship types:

- **Many-to-many** needs an extra "link" table with two foreign keys (use `link_model=` in `Relationship`).
- **One-to-one** is a one-to-many where the "many" side is limited to one (using `uselist=False` through `sa_relationship_kwargs`).

Beware of the **N+1 query problem**: loading a list of 100 heroes and then touching `hero.team` for each can trigger 101 queries. Use eager loading options from SQLAlchemy (such as `selectinload`) when you know you need the related objects.

---

## 12.4 Dependency-injected sessions

Lesson 6 introduced dependencies with `yield`. Sessions are their most important use:

```python
def get_session():
    with Session(engine) as session:
        yield session

SessionDep = Annotated[Session, Depends(get_session)]
```

Benefits:

1. **One session per request**, opened when the request starts and closed after the response, even on errors.
2. **No global session** shared between requests (sessions are not thread-safe).
3. Endpoints just declare `session: SessionDep`, so they stay short.
4. **Easy testing**: replace `get_session` with one that uses a test database (Lesson 16).
5. Sub-dependencies can reuse the same session within a request thanks to dependency caching.

A dependency that builds on the session:

```python
def get_hero_or_404(hero_id: int, session: SessionDep) -> Hero:
    hero = session.get(Hero, hero_id)
    if not hero:
        raise HTTPException(status_code=404, detail="Hero not found")
    return hero


HeroDep = Annotated[Hero, Depends(get_hero_or_404)]


@app.get("/v2/heroes/{hero_id}", response_model=HeroPublic)
def read_hero_v2(hero: HeroDep):
    return hero
```

**Plain SQLAlchemy equivalent.** If you prefer SQLAlchemy directly, the pattern is the same:

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import DeclarativeBase, Mapped, Session, mapped_column, sessionmaker


class Base(DeclarativeBase):
    pass


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(unique=True)


engine = create_engine("sqlite:///app.db", connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine, autoflush=False)


def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()


@app.get("/users/{user_id}")
def read_user(user_id: int, db: Annotated[Session, Depends(get_db)]):
    user = db.get(User, user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return {"id": user.id, "email": user.email}
```

With SQLAlchemy you must create separate **Pydantic schemas** for the API, whereas SQLModel lets you reuse the same class style for both. Both are good choices; SQLModel is simpler to start with, SQLAlchemy offers the most control.

---

[Previous](./[11]-WebSockets.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[13]-Migrations-And-Async-Databases.md)
