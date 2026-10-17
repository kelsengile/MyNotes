[Previous](./[13]-Middleware-And-Request-Handling.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[15]-Authentication-And-Security.md)

*Building The Backend*

# Lesson 14 - Working With Databases

Almost every real application needs to remember things after it restarts: users, orders, posts, settings. That is the job of a **database**. Frameworks do not replace databases, but they provide structured ways to connect to them, query them, evolve their structure over time, and describe your data as code. This lesson covers all four.

## 14.1 Connecting a Framework to a Database

### Kinds of databases

| Type | How data is organized | Examples | Good fit for |
|---|---|---|---|
| **Relational (SQL)** | Tables of rows and columns, linked by relationships | PostgreSQL, MySQL, SQLite, SQL Server | Structured data with clear relationships; the most common choice |
| **Document (NoSQL)** | Flexible JSON-like documents | MongoDB | Data whose shape varies or changes often |
| **Key-value / in-memory** | Simple key to value lookups | Redis | Caching, sessions, fast counters |

Most framework tutorials use a relational database, and SQL (Structured Query Language) is the language used to talk to them. **SQLite** is especially handy for learning and small projects because it stores everything in a single file and needs no server.

### The pieces of a connection

To use a database, your application needs a **driver** (a library that speaks the database's protocol), and connection details. Those details are often combined into a **connection string** (or *database URL*):

```text
postgresql://username:password@hostname:5432/database_name
└─ kind ──┘   └─ credentials ─┘ └─ host ─┘└port┘└─ which database ─┘
```

Because a connection string contains a password and differs between environments, it belongs in an **environment variable**, never in your source code (Lesson 7).

Frameworks have a standard place for this configuration:

| Framework | Where database settings go |
|---|---|
| Django | The `DATABASES` setting in `settings.py` |
| Laravel | `DB_*` variables in `.env`, read by `config/database.php` |
| Spring Boot | `spring.datasource.*` in `application.properties` or `application.yml` |
| Rails | `config/database.yml` |
| ASP.NET Core | A connection string in configuration |
| Express / Flask / FastAPI | Whatever you choose; typically a `DATABASE_URL` variable passed to a database library |

### Connection pooling

Opening a database connection is relatively slow. Instead of opening a new one for every request, frameworks and drivers keep a **pool** of connections that are reused. You usually only configure its size, and the framework does the rest.

### Talking to a database directly

Here is the lowest-level way to use a database, with Python's built-in SQLite support. It creates a table, inserts a row, and reads it back:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute(
    "CREATE TABLE tasks ("
    "id INTEGER PRIMARY KEY, "
    "title TEXT NOT NULL, "
    "done INTEGER NOT NULL DEFAULT 0)"
)

# Placeholders (?) keep data separate from the SQL text
conn.execute("INSERT INTO tasks (title) VALUES (?)", ("Learn databases",))
conn.commit()

for row in conn.execute("SELECT id, title, done FROM tasks WHERE done = ?", (0,)):
    print(row)

conn.close()
```

Output:

```text
(1, 'Learn databases', 0)
```

Notice the `?` placeholders. Values are passed **separately** from the SQL text, which protects against **SQL injection**, a serious vulnerability covered in Lesson 15. **Never build SQL by pasting user input into a string.**

---

## 14.2 ORMs and Query Builders

Writing raw SQL strings works, but in a large application it becomes repetitive, easy to get wrong, and awkward to mix with object-oriented code. There are three common levels of abstraction.

### The three levels

| Level | What you write | Example tools |
|---|---|---|
| **Raw SQL** | SQL text sent to the driver | `sqlite3`, `psycopg`, JDBC |
| **Query builder** | Method calls that *build* SQL | Knex, jOOQ |
| **ORM** | Classes and objects that map to tables | Django ORM, SQLAlchemy, Hibernate / JPA, Eloquent (Laravel), ActiveRecord (Rails), Entity Framework, Prisma, TypeORM, Sequelize |

The same question, "find the tasks that are not done," at each level (illustrative):

```text
SQL:            SELECT * FROM tasks WHERE done = 0;

Query builder:  db("tasks").where({ done: false })

ORM:            Task.objects.filter(done=False)        (Django style)
                session.scalars(select(Task).where(...))  (SQLAlchemy style)
```

### What an ORM does

An **ORM (Object-Relational Mapper)** maps:

| Database world | Code world |
|---|---|
| A **table** | A **class** |
| A **row** | An **object** (instance) |
| A **column** | A **property** |

You work with ordinary objects, and the ORM generates and runs the SQL. Here it is in practice with SQLAlchemy, a popular Python ORM:

```python
from sqlalchemy import String, create_engine, select
from sqlalchemy.orm import DeclarativeBase, Mapped, Session, mapped_column


class Base(DeclarativeBase):
    pass


class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(100))
    done: Mapped[bool] = mapped_column(default=False)


engine = create_engine("sqlite:///:memory:")
Base.metadata.create_all(engine)  # creates the table from the class

with Session(engine) as session:
    session.add(Task(title="Learn ORMs"))
    session.commit()

    for task in session.scalars(select(Task).where(Task.done.is_(False))):
        print(task.id, task.title)
```

Output:

```text
1 Learn ORMs
```

Notice that no SQL was written. The `Task` class describes the table, `create_all` created it, and `select(Task).where(...)` expressed the query.

### Strengths and weaknesses

| Strength | Weakness |
|---|---|
| Less repetitive code | Generated SQL can be inefficient if you are not careful |
| Works with the database portably, so switching databases is easier | A learning curve on top of SQL itself |
| Helps prevent SQL injection by default | Complex queries can be awkward to express |
| Models double as documentation | "Magic" can hide what is really happening |

### The N+1 query problem

The most famous ORM pitfall is the **N+1 problem**. Imagine listing 100 users along with the number of tasks each owns. A careless approach runs one query to fetch the users, then **one more query per user** to fetch their tasks, so 101 queries in total:

```text
# Pseudocode: slow
users = fetch_all_users()                # 1 query
for user in users:
    print(user.name, len(user.tasks))    # 1 extra query PER user (100 more)
```

Every ORM provides a way to **eager load** related data in one or two queries, for example `select_related` and `prefetch_related` in Django, `joinedload` in SQLAlchemy, and `with()` in Laravel. When a page is mysteriously slow, check how many queries it runs.

### The wise approach

Use the ORM for everyday work, learn enough SQL to understand what the ORM generates, and drop down to raw SQL (still with placeholders) for the occasional query the ORM handles poorly.

---

## 14.3 Migrations

Your database structure (the **schema**) changes as your application grows: a new table, a new column, a renamed field. A **migration** is a small, versioned file that describes *one change* to the schema, and the framework applies them in order.

### Why not just edit the database by hand?

- Your teammates' databases and the production database would drift apart from yours.
- There is no history of what changed, when, or why.
- You could not recreate the database from scratch reliably.

Migrations solve this by treating the schema like code: **stored in version control, reviewed, and applied the same way everywhere.**

### What a migration looks like

In its simplest form, a migration is a numbered file. Here are two:

`migrations/001_create_tasks.sql`

```sql
CREATE TABLE tasks (
    id    INTEGER PRIMARY KEY,
    title TEXT NOT NULL
);
```

`migrations/002_add_due_date.sql`

```sql
ALTER TABLE tasks ADD COLUMN due_date TEXT;
```

A migration tool keeps a record of which migrations have already been applied, and applies only the new ones. Here is a miniature version that shows the mechanism:

```python
import sqlite3
from pathlib import Path

conn = sqlite3.connect("app.db")
conn.execute("CREATE TABLE IF NOT EXISTS schema_migrations (version TEXT PRIMARY KEY)")
applied = {row[0] for row in conn.execute("SELECT version FROM schema_migrations")}

for path in sorted(Path("migrations").glob("*.sql")):
    if path.name in applied:
        continue  # already applied: never run twice
    conn.executescript(path.read_text())
    conn.execute("INSERT INTO schema_migrations (version) VALUES (?)", (path.name,))
    conn.commit()
    print("Applied", path.name)

print("Schema is up to date")
```

The first run applies both files:

```text
Applied 001_create_tasks.sql
Applied 002_add_due_date.sql
Schema is up to date
```

Running it again does nothing, because both are already recorded:

```text
Schema is up to date
```

Real tools work the same way, but add much more: they can *generate* migrations by comparing your models to the database, and can *roll back* ("down" migrations).

### Migration tools by framework

| Framework / ecosystem | Typical commands |
|---|---|
| Django | `python manage.py makemigrations`, then `python manage.py migrate` |
| Laravel | `php artisan make:migration ...`, then `php artisan migrate` |
| Rails | `rails generate migration ...`, then `rails db:migrate` |
| ASP.NET Core (Entity Framework) | `dotnet ef migrations add ...`, then `dotnet ef database update` |
| SQLAlchemy | The Alembic tool |
| Java | Flyway or Liquibase |
| Prisma / TypeORM / Sequelize | Their built-in migration commands |

### Migration rules

1. **Never edit a migration that has already been applied** anywhere other than your own machine. Create a *new* migration to change things further.
2. **Keep each migration small** and focused on one change.
3. **Commit migrations with the code that needs them.**
4. **Test migrations on a copy of real data** before running them in production, and take a **backup** first.
5. Be careful with **destructive changes** such as dropping columns. Data removed is gone. Often, a safe sequence is: stop using it, deploy, and only then remove it.
6. Some frameworks also support **seed data**: scripts that insert starting records, such as default roles or sample data for development.

---

## 14.4 Models and Data Validation

### Models

A **model** is the code description of one kind of data in your application: its fields, their types, rules, and relationships. In MVC terms (Lesson 6), this is the "M". In an ORM, the model is also what links to a database table.

A model typically defines:

- **Fields** and their **types** (text, integer, date, boolean).
- **Constraints**, such as *required*, *unique*, and *maximum length*.
- **Relationships** to other models.
- Sometimes **behavior**, such as calculated values.

### Relationships

Data is rarely isolated. Relationships describe how records connect:

| Relationship | Meaning | Example |
|---|---|---|
| **One-to-many** | One record is linked to many others | One user has many tasks |
| **Many-to-many** | Records on both sides can link to many | Tasks and tags: a task has many tags, and a tag marks many tasks |
| **One-to-one** | One record is linked to exactly one other | A user and their profile |

In a relational database, a one-to-many relationship is stored with a **foreign key**: each task holds the `id` of its user.

Here are two models with a one-to-many relationship and a validation rule, again in SQLAlchemy:

```python
from sqlalchemy import ForeignKey, String, create_engine
from sqlalchemy.orm import (
    DeclarativeBase, Mapped, Session, mapped_column, relationship, validates,
)


class Base(DeclarativeBase):
    pass


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(String(255), unique=True)
    tasks: Mapped[list["Task"]] = relationship(back_populates="owner")

    @validates("email")
    def validate_email(self, key, value):
        if "@" not in value:
            raise ValueError("Invalid email address")
        return value


class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(100))
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    owner: Mapped[User] = relationship(back_populates="tasks")


engine = create_engine("sqlite:///:memory:")
Base.metadata.create_all(engine)

with Session(engine) as session:
    ada = User(email="ada@example.com")
    ada.tasks.append(Task(title="Write the first program"))
    session.add(ada)
    session.commit()
    print(ada.tasks[0].title, "belongs to", ada.tasks[0].owner.email)

    try:
        User(email="not-an-email")
    except ValueError as error:
        print("Rejected:", error)
```

Output:

```text
Write the first program belongs to ada@example.com
Rejected: Invalid email address
```

The `ForeignKey("users.id")` column stores the link in the database, while `relationship` lets you move between objects naturally (`ada.tasks`, `task.owner`) without writing joins by hand.

### Validation at two levels

Data can be checked in two places, and robust applications use **both**:

| Level | What it checks | Why it matters |
|---|---|---|
| **Application (model) validation** | Rules written in code: "email must contain @" | Gives friendly, specific error messages before touching the database |
| **Database constraints** | Rules enforced by the database: `NOT NULL`, `UNIQUE`, foreign keys, `CHECK` | The last line of defense: protects the data even if the application has a bug, or if another program writes to the database |

In the example above, `unique=True` on the email is a database-level constraint, while the `@validates` method is application-level.

### Common database constraints

| Constraint | Effect |
|---|---|
| `PRIMARY KEY` | Uniquely identifies each row |
| `NOT NULL` | The column must have a value |
| `UNIQUE` | No two rows may share the value |
| `FOREIGN KEY` | The value must refer to an existing row elsewhere |
| `CHECK` | The value must satisfy a condition |
| `DEFAULT` | The value used when none is provided |

### Putting it together

Notice how this connects to earlier lessons. A request arrives, middleware parses it, request validation (Lesson 13) rejects bad shapes, the model's rules reject invalid data, and database constraints stand guard at the end. Each layer catches different problems, and together they keep your data trustworthy.

---

[Previous](./[13]-Middleware-And-Request-Handling.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[15]-Authentication-And-Security.md)
