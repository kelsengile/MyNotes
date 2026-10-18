[Previous](./[10]-Databases-With-SQLAlchemy.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[12]-Building-JSON-APIs.md)

*Data*

# Lesson 11 - Migrations With Flask-Migrate

---

## 11.1 Alembic Basics

### The problem

`db.create_all()` builds tables from your models, but it **cannot change** tables that already exist. Suppose your app is live with real data, and you add a `phone` column to `User`. `create_all()` will not add it. Deleting and recreating the database would destroy user data.

### The solution: migrations

A **migration** is a small, versioned script that describes **one step** of changing the database schema, such as adding a column or creating a table. Each migration can be applied (**upgrade**) and reversed (**downgrade**). Together they form a history of how your database evolved, much like Git commits do for code.

### The tools

| Tool | Role |
|---|---|
| **Alembic** | The migration engine made by the SQLAlchemy author. It compares models to the database and generates and runs migration scripts. |
| **Flask-Migrate** | A thin extension that integrates Alembic into Flask and gives you `flask db ...` commands. |

### Installation and setup

```bash
pip install Flask-Migrate
```

```python
# myapp/extensions.py
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

db = SQLAlchemy()
migrate = Migrate()
```

```python
# myapp/__init__.py
from flask import Flask
from .extensions import db, migrate


def create_app(config_object="config.DevelopmentConfig"):
    app = Flask(__name__)
    app.config.from_object(config_object)

    db.init_app(app)
    migrate.init_app(app, db)

    from . import models   # make sure models are imported so Alembic can see them

    return app
```

> **Important:** Alembic can only detect models that have been **imported**. Importing your `models` module inside the factory ensures they are registered.

### Initialize the migration folder (once per project)

```bash
flask --app myapp db init
```

This creates a `migrations/` folder:

```
migrations/
├── alembic.ini
├── env.py
├── script.py.mako
└── versions/        # your migration scripts will appear here
```

Commit the entire `migrations/` folder to version control.

### How Alembic tracks state

Alembic stores the **current revision ID** in a table named `alembic_version` inside your database. By comparing that ID to the scripts in `versions/`, it knows which migrations have already been applied.

---

## 11.2 Creating Migrations

### Workflow overview

1. Change your models.
2. **Generate** a migration script.
3. **Review** (and if needed edit) the script.
4. **Apply** it to the database.

### Generating the first migration

Suppose you already have a `User` model:

```python
class User(db.Model):
    id: Mapped[int] = mapped_column(primary_key=True)
    username: Mapped[str] = mapped_column(String(80), unique=True)
```

Run:

```bash
flask --app myapp db migrate -m "create user table"
```

Alembic compares your models to the database and writes a script in `migrations/versions/`, such as `a1b2c3d4e5f6_create_user_table.py`.

### Anatomy of a migration script

```python
"""create user table

Revision ID: a1b2c3d4e5f6
Revises:
Create Date: 2025-01-15 10:30:00
"""
from alembic import op
import sqlalchemy as sa

revision = "a1b2c3d4e5f6"
down_revision = None          # the previous migration (None for the first)
branch_labels = None
depends_on = None


def upgrade():
    op.create_table(
        "user",
        sa.Column("id", sa.Integer(), nullable=False),
        sa.Column("username", sa.String(length=80), nullable=False),
        sa.PrimaryKeyConstraint("id"),
        sa.UniqueConstraint("username"),
    )


def downgrade():
    op.drop_table("user")
```

| Part | Meaning |
|---|---|
| `revision` | This migration's unique ID. |
| `down_revision` | The ID of the migration that comes before it, forming a chain. |
| `upgrade()` | Steps to move the database **forward**. |
| `downgrade()` | Steps to **undo** this migration. |

### Making a change later

Add a column to the model:

```python
class User(db.Model):
    id: Mapped[int] = mapped_column(primary_key=True)
    username: Mapped[str] = mapped_column(String(80), unique=True)
    phone: Mapped[str | None] = mapped_column(String(20))
```

Generate a new migration:

```bash
flask --app myapp db migrate -m "add phone to user"
```

Its `upgrade()` will contain something like:

```python
def upgrade():
    op.add_column("user", sa.Column("phone", sa.String(length=20), nullable=True))
```

### Always review auto-generated scripts

Autogeneration is a **best effort**, not magic. It can miss or misinterpret some changes. Watch for:

- **Renames** — a renamed column or table is detected as a *drop plus add*, which **loses data**. Edit the script to use `op.alter_column(..., new_column_name=...)` or `op.rename_table(...)`.
- **New `NOT NULL` columns** on tables with existing rows — the database needs a default value, or the upgrade will fail. Add `server_default=...`, or add the column as nullable, fill it, then make it required.
- **Constraints and indexes** — check they were included.

### Data migrations

Migrations may also move or transform data, using `op.execute(...)`:

```python
def upgrade():
    op.add_column("user", sa.Column("is_active", sa.Boolean(), server_default=sa.true(), nullable=False))
    op.execute("UPDATE user SET is_active = TRUE WHERE username IS NOT NULL")
```

### SQLite note

SQLite has limited support for altering tables. Flask-Migrate enables Alembic's **batch mode** by default, which rebuilds a table behind the scenes to apply changes. If you ever see errors altering SQLite tables, make sure batch mode (`render_as_batch=True`) is active.

---

## 11.3 Applying And Reverting

### Upgrading the database

```bash
flask --app myapp db upgrade
```

Applies **all** pending migrations in order, up to the latest ("head").

You can also target a specific revision:

```bash
flask --app myapp db upgrade a1b2c3d4e5f6
flask --app myapp db upgrade +1          # apply the next one only
```

### Downgrading

```bash
flask --app myapp db downgrade           # step back one migration
flask --app myapp db downgrade -2        # step back two
flask --app myapp db downgrade base      # undo everything (empty database)
```

Downgrading runs each migration's `downgrade()` function. Dropping a column also drops its data, so be careful, especially in production.

### Inspecting the state

| Command | What it shows |
|---|---|
| `flask db current` | The revision the database is currently at. |
| `flask db history` | The full list of migrations in order. |
| `flask db history --verbose` | History with more detail. |
| `flask db heads` | The latest revision(s) in the scripts. |
| `flask db show <revision>` | Details of one migration. |

(Always include `--app myapp` before `db`, or set `FLASK_APP`, as shown above.)

### Typical development cycle

```bash
# 1. Edit models.py
# 2. Generate
flask --app myapp db migrate -m "add phone to user"
# 3. Open the generated file in migrations/versions/ and review it
# 4. Apply
flask --app myapp db upgrade
```

### If something went wrong

- **Bad migration, not yet shared with anyone:** run `flask db downgrade`, delete the migration file, fix your model, and generate it again.
- **Bad migration already applied elsewhere:** do **not** edit or delete it. Create a **new** migration that corrects the problem.

### Stamping

`flask db stamp <revision>` marks the database as being at a revision **without running anything**. Use it when the database already has the right structure (for example, adopting migrations in an existing project):

```bash
flask --app myapp db stamp head
```

### Deploying migrations

In production, apply migrations as part of each deployment, **before** the new code starts serving traffic:

```bash
flask --app myapp db upgrade
```

Always take a **database backup** first.

---

## 11.4 Team Workflows

Migrations are shared code. Working on them in a team needs a few conventions.

### Commit migrations to version control

- Commit the whole `migrations/` folder.
- Commit each migration **together with** the model change that caused it.
- Never ignore `migrations/versions/` in `.gitignore`.

### When a teammate pulls your changes

```bash
git pull
flask --app myapp db upgrade
```

Their local database is brought up to date with the new migrations.

### Never edit applied migrations

Once a migration has been shared or applied to any other database, treat it as **immutable**. Editing it would leave databases in different states. Instead, add a new migration.

### Multiple heads: when two people branch

Two developers might each create a migration from the same parent at the same time:

```
      A
     / \
    B   C       <- two heads
```

When this is merged, Alembic complains about **multiple heads**. Fix it by creating a **merge migration**:

```bash
flask --app myapp db heads                    # shows both heads
flask --app myapp db merge -m "merge heads" <revision1> <revision2>
flask --app myapp db upgrade
```

The merge migration has two parents and ties the history back into a single line.

### Reducing conflicts

- Pull the latest code and run `db upgrade` **before** generating a new migration.
- Merge or rebase frequently so migrations are created from the latest history.
- Keep each migration small and focused, with a clear `-m` message.
- Announce large schema changes to the team.

### Safe deployment practices

| Practice | Why |
|---|---|
| Back up the production database first | You can restore if a migration fails. |
| Test migrations on a copy of production data | Finds slow or failing steps early. |
| Make changes **backward compatible** | Old code can keep running while the new schema rolls out. |
| Avoid long locks on large tables | Big `ALTER TABLE` operations can block your app. |
| Split risky changes into multiple deployments | For example: add a nullable column, deploy, backfill, then add constraints. |

### Example of a safe multi-step change (renaming a column)

1. **Deploy 1:** add the new column; write to both old and new.
2. **Backfill:** copy old values into the new column.
3. **Deploy 2:** read from the new column only.
4. **Deploy 3:** drop the old column.

### Quick reference

```bash
flask --app myapp db init                     # once per project
flask --app myapp db migrate -m "message"     # generate a migration
flask --app myapp db upgrade                  # apply migrations
flask --app myapp db downgrade                # undo one migration
flask --app myapp db current                  # show current revision
flask --app myapp db history                  # list all migrations
flask --app myapp db merge -m "msg" r1 r2     # merge two heads
```

---

[Previous](./[10]-Databases-With-SQLAlchemy.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[12]-Building-JSON-APIs.md)
