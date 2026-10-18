[Previous](./[9]-Error-Handling-And-Logging.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[11]-Migrations-With-Flask-Migrate.md)

*Data*

# Lesson 10 - Databases With SQLAlchemy

---

## 10.1 Flask-SQLAlchemy

**SQLAlchemy** is Python's most popular database toolkit. Its **ORM** (Object-Relational Mapper) lets you work with database tables as Python classes and rows as Python objects instead of writing raw SQL.

**Flask-SQLAlchemy** is the extension that connects SQLAlchemy to Flask. It manages database connections, ties sessions to the request lifecycle, and adds handy helpers.

### Installation

```bash
pip install Flask-SQLAlchemy
```

This lesson uses the modern **SQLAlchemy 2.0 style** supported by Flask-SQLAlchemy 3.x.

### Setting it up

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.orm import DeclarativeBase


class Base(DeclarativeBase):
    pass


db = SQLAlchemy(model_class=Base)

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///app.db"
db.init_app(app)
```

- `Base` is the parent class for all models.
- `db` is created **without** an app, then connected with `db.init_app(app)`, matching the factory pattern from Lesson 6.
- With SQLite, `sqlite:///app.db` creates the file inside the app's **instance folder**.

### Database URIs

| Database | Example URI |
|---|---|
| SQLite | `sqlite:///app.db` |
| PostgreSQL | `postgresql://user:password@localhost:5432/mydb` |
| MySQL | `mysql+pymysql://user:password@localhost/mydb` |

Each non-SQLite database needs a driver package (for example `psycopg2-binary` for PostgreSQL or `PyMySQL` for MySQL).

### Creating the tables

For quick experiments you can create all tables from your models:

```python
with app.app_context():
    db.create_all()
```

> `create_all()` only creates **missing** tables. It never alters existing ones. For real projects, use migrations (Lesson 11).

### The application context

Database operations need an **application context**, which exists automatically during requests. Outside a request (scripts, shells) you must create one with `with app.app_context():`.

### Key concepts

| Term | Meaning |
|---|---|
| **Model** | A Python class that represents a table. |
| **Column** | A field on the model, representing a table column. |
| **Session** (`db.session`) | A workspace that tracks your changes until you commit. |
| **Query** | A request for rows, built with `db.select(...)`. |

---

## 10.2 Models And Relationships

### Defining a model

```python
from datetime import datetime, timezone
from sqlalchemy import String, Text, ForeignKey
from sqlalchemy.orm import Mapped, mapped_column, relationship


class User(db.Model):
    id: Mapped[int] = mapped_column(primary_key=True)
    username: Mapped[str] = mapped_column(String(80), unique=True)
    email: Mapped[str] = mapped_column(String(120), unique=True)
    created_at: Mapped[datetime] = mapped_column(
        default=lambda: datetime.now(timezone.utc)
    )

    posts: Mapped[list["Post"]] = relationship(back_populates="author")

    def __repr__(self):
        return f"<User {self.username}>"


class Post(db.Model):
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(200))
    body: Mapped[str] = mapped_column(Text)
    created_at: Mapped[datetime] = mapped_column(
        default=lambda: datetime.now(timezone.utc)
    )
    user_id: Mapped[int] = mapped_column(ForeignKey("user.id"))

    author: Mapped["User"] = relationship(back_populates="posts")
```

### Understanding the syntax

| Part | Meaning |
|---|---|
| `Mapped[int]` | The Python type of the attribute. Also makes the column `NOT NULL` by default. |
| `Mapped[str \| None]` or `Mapped[Optional[str]]` | A nullable column. |
| `mapped_column(...)` | Configures the column: `primary_key`, `unique`, `default`, `index`, and so on. |
| `String(80)` | A text column with a maximum length. |
| `Text` | A long text column with no fixed length. |
| `ForeignKey("user.id")` | Links to the `id` column of the `user` table. |

Flask-SQLAlchemy names tables automatically by converting the class name to snake case: `User` becomes `user`, and `BlogPost` becomes `blog_post`.

### Relationships

A **relationship** lets you navigate between related objects without writing joins by hand.

- One **user** has many **posts** (one-to-many).
- Each **post** belongs to one **author**.

The foreign key (`Post.user_id`) lives on the "many" side. The `relationship()` attributes on both sides are linked with `back_populates`.

```python
user = db.session.get(User, 1)
print(user.posts)           # list of Post objects
post = user.posts[0]
print(post.author.username) # navigates back to the user
```

### Many-to-many relationships

Use an **association table**:

```python
from sqlalchemy import Table, Column, ForeignKey

post_tags = Table(
    "post_tags",
    db.metadata,
    Column("post_id", ForeignKey("post.id"), primary_key=True),
    Column("tag_id", ForeignKey("tag.id"), primary_key=True),
)


class Tag(db.Model):
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50), unique=True)

    posts: Mapped[list["Post"]] = relationship(
        secondary=post_tags, back_populates="tags"
    )
```

Then add this to `Post`:

```python
    tags: Mapped[list["Tag"]] = relationship(
        secondary=post_tags, back_populates="posts"
    )
```

### Deleting related rows

To delete a user's posts automatically when the user is deleted, set a cascade:

```python
posts: Mapped[list["Post"]] = relationship(
    back_populates="author", cascade="all, delete-orphan"
)
```

---

## 10.3 Querying

Queries in SQLAlchemy 2.0 are built with `db.select()` and run through the session.

### Creating rows

```python
user = User(username="ada", email="ada@example.com")
db.session.add(user)
db.session.commit()

post = Post(title="Hello", body="My first post", author=user)
db.session.add(post)
db.session.commit()
```

### Getting one row by primary key

```python
user = db.session.get(User, 1)       # returns None if not found
```

In a view, `db.get_or_404` returns a 404 error automatically when the row is missing:

```python
@app.route("/users/<int:user_id>")
def show_user(user_id):
    user = db.get_or_404(User, user_id)
    return user.username
```

### Selecting rows

```python
# All users
users = db.session.scalars(db.select(User)).all()

# Filtering
ada = db.session.scalars(
    db.select(User).where(User.username == "ada")
).first()

# Multiple conditions (AND)
recent = db.session.scalars(
    db.select(Post)
    .where(Post.user_id == 1, Post.title.like("%Flask%"))
).all()

# Ordering and limiting
latest = db.session.scalars(
    db.select(Post).order_by(Post.created_at.desc()).limit(5)
).all()
```

`db.session.scalars(...)` returns model objects. `.all()` gives a list, `.first()` gives one object or `None`, and `.one()` raises an error unless there is exactly one result.

### Handy 404 helpers

```python
post = db.first_or_404(db.select(Post).where(Post.title == "Hello"))
```

### Filtering helpers

| Expression | Meaning |
|---|---|
| `User.id == 5` | Equal |
| `User.id != 5` | Not equal |
| `User.name.like("A%")` | SQL `LIKE` |
| `User.name.ilike("a%")` | Case-insensitive `LIKE` |
| `User.id.in_([1, 2, 3])` | In a list |
| `User.email.is_(None)` | Is `NULL` |
| `db.or_(A, B)` / `db.and_(A, B)` | Combine conditions |

### Counting

```python
from sqlalchemy import func

total = db.session.scalar(db.select(func.count()).select_from(User))
```

### Pagination

```python
@app.route("/posts")
def posts():
    page = request.args.get("page", 1, type=int)
    pagination = db.paginate(
        db.select(Post).order_by(Post.created_at.desc()),
        page=page,
        per_page=10,
        error_out=False,
    )
    return render_template("posts.html", pagination=pagination)
```

The `pagination` object provides `.items`, `.page`, `.pages`, `.has_next`, `.has_prev`, and `.iter_pages()` for building page links.

### Joining tables

Relationships usually remove the need for explicit joins, but you can write them:

```python
rows = db.session.execute(
    db.select(User.username, Post.title).join(Post, Post.user_id == User.id)
).all()
```

### Avoiding the N+1 query problem

Looping over many users and accessing `user.posts` can trigger one extra query per user. Load related data up front:

```python
from sqlalchemy.orm import selectinload

users = db.session.scalars(
    db.select(User).options(selectinload(User.posts))
).all()
```

---

## 10.4 Sessions And Transactions

The `db.session` is a **unit of work**. Changes you make are collected in memory and only written to the database when you **commit**.

### The typical flow

```python
user = User(username="grace", email="grace@example.com")
db.session.add(user)       # stage the new object
db.session.commit()        # write to the database permanently
```

### Updating

Change attributes on a loaded object, then commit:

```python
user = db.session.get(User, 1)
user.email = "new@example.com"
db.session.commit()
```

### Deleting

```python
post = db.session.get(Post, 3)
db.session.delete(post)
db.session.commit()
```

### Rolling back on errors

A **transaction** is a group of changes that succeed or fail **together**. If something goes wrong, undo everything since the last commit:

```python
from sqlalchemy.exc import IntegrityError


@app.post("/register")
def register():
    user = User(username="ada", email="ada@example.com")
    db.session.add(user)
    try:
        db.session.commit()
    except IntegrityError:
        db.session.rollback()
        return {"error": "Username or email already exists"}, 409
    return {"id": user.id}, 201
```

After an exception, the session cannot be used until you call `rollback()`.

### Several changes, one commit

```python
order = Order(user_id=1)
db.session.add(order)
db.session.flush()           # sends SQL, so order.id is now available (not yet committed)

item = OrderItem(order_id=order.id, product="Book", quantity=2)
db.session.add(item)

db.session.commit()          # both rows are saved together, or neither is
```

| Method | What it does |
|---|---|
| `add(obj)` / `add_all([...])` | Stage new objects. |
| `delete(obj)` | Stage a deletion. |
| `flush()` | Send pending SQL without ending the transaction. Assigns generated IDs. |
| `commit()` | Save all changes permanently. |
| `rollback()` | Discard uncommitted changes. |

### Session lifetime in Flask

Flask-SQLAlchemy creates one session per application context and **removes it automatically** when the request ends. You do not need to close it yourself.

### Helpful practices

- Keep transactions **short**; commit as soon as a unit of work is complete.
- Never commit inside loops unless you truly need each row saved separately.
- Validate input (Lesson 5) **before** touching the database.
- Use database constraints (`unique=True`, `nullable`, foreign keys) as the last line of defense.
- Never build SQL by concatenating user input. The ORM and parameterized queries protect you from SQL injection; see Lesson 14.

### A small complete example

```python
@app.post("/posts")
def create_post():
    data = request.get_json()
    author = db.get_or_404(User, data["user_id"])
    post = Post(title=data["title"], body=data["body"], author=author)
    db.session.add(post)
    db.session.commit()
    return {"id": post.id, "title": post.title}, 201
```

---

[Previous](./[9]-Error-Handling-And-Logging.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[11]-Migrations-With-Flask-Migrate.md)
