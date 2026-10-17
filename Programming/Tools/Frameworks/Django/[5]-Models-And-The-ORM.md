[Previous](./[4]-Templates.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[6]-Migrations.md)

*Data*

# Lesson 5 - Models And The ORM

---

## 5.1 Defining models

A **model** is a Python class that describes one table in your database. Each attribute on the class is a **field**, which becomes a column. The **ORM** (Object-Relational Mapper) translates between Python objects and database rows so you rarely write SQL by hand.

### Your first model

Models live in an app's `models.py` and inherit from `django.db.models.Model`:

```python
# blog/models.py
from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    body = models.TextField()
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

This describes a table with these columns (plus an automatic primary key `id`):

| Column | Type |
|---|---|
| `id` | Auto-incrementing integer, added automatically |
| `title` | Short text, up to 200 characters |
| `slug` | URL-friendly text, must be unique |
| `body` | Long text |
| `published` | True/False |
| `created_at` | Set once when the row is created |
| `updated_at` | Updated every time the row is saved |

### From model to table

Defining the class does **not** create the table. You must create and apply a **migration** (Lesson 6):

```bash
python manage.py makemigrations
python manage.py migrate
```

### The primary key

If you do not define a primary key, Django adds `id = models.BigAutoField(primary_key=True)` automatically (the type is controlled by `DEFAULT_AUTO_FIELD`). To use a different field as the primary key, set `primary_key=True` on it.

### Naming conventions

Model class names are **singular** and use PascalCase (`Post`, not `Posts`). Django names the table `appname_modelname` in lowercase, so `Post` in the `blog` app becomes `blog_post`.

---

## 5.2 Field types and options

### Common field types

| Field | Use for |
|---|---|
| `CharField(max_length=...)` | Short text; `max_length` is required |
| `TextField()` | Long text without a length limit |
| `IntegerField()`, `BigIntegerField()`, `PositiveIntegerField()` | Whole numbers |
| `FloatField()` | Floating-point numbers |
| `DecimalField(max_digits=, decimal_places=)` | Exact decimals, ideal for money |
| `BooleanField()` | True or False |
| `DateField()`, `TimeField()`, `DateTimeField()` | Dates and times |
| `EmailField()` | Text validated as an email address |
| `URLField()` | Text validated as a URL |
| `SlugField()` | Letters, numbers, hyphens, underscores |
| `UUIDField()` | Universally unique identifiers |
| `JSONField()` | JSON-serializable data |
| `FileField(upload_to=...)` | File uploads |
| `ImageField(upload_to=...)` | Image uploads (requires the Pillow package) |

Use `DecimalField` rather than `FloatField` for prices to avoid rounding errors:

```python
price = models.DecimalField(max_digits=8, decimal_places=2)
```

### Field options

Every field accepts options that change its behavior:

| Option | Meaning |
|---|---|
| `null=True` | The database column may store `NULL` |
| `blank=True` | The field may be left empty in forms and validation |
| `default=...` | Value used when none is given (can be a callable) |
| `unique=True` | No two rows may share the same value |
| `choices=...` | Restricts the value to a fixed set of options |
| `db_index=True` | Creates a database index to speed up lookups |
| `help_text="..."` | Extra text shown in forms and the admin |
| `verbose_name="..."` | Human-friendly field label |
| `editable=False` | Hidden from forms and the admin |

### null vs blank

These are frequently confused:

- `null` is **database-related**: can the column hold `NULL`?
- `blank` is **validation-related**: can a form leave it empty?

For text fields (`CharField`, `TextField`), prefer `blank=True` **without** `null=True`. Django then stores an empty string, which avoids having two different "empty" values. For non-text fields like dates or numbers, an optional field usually needs both: `null=True, blank=True`.

### Choices

The clean way to define choices is with `TextChoices` (or `IntegerChoices`):

```python
class Post(models.Model):
    class Status(models.TextChoices):
        DRAFT = "draft", "Draft"
        PUBLISHED = "published", "Published"
        ARCHIVED = "archived", "Archived"

    status = models.CharField(
        max_length=10,
        choices=Status.choices,
        default=Status.DRAFT,
    )
```

The first value (`"draft"`) is stored in the database; the second (`"Draft"`) is the human-readable label. Use it in code as `Post.Status.PUBLISHED`. For a post instance, `post.get_status_display()` returns the label.

### Automatic date fields

- `auto_now_add=True` sets the value **once**, when the object is first created.
- `auto_now=True` updates the value **every time** the object is saved.

---

## 5.3 Creating and saving objects

Open the Django shell to experiment with your models:

```bash
python manage.py shell
```

### Creating

```python
from blog.models import Post

# Option 1: build, then save
post = Post(title="Hello", slug="hello", body="My first post")
post.save()

# Option 2: create in one step
post = Post.objects.create(title="Second", slug="second", body="More text")
```

After saving, the object has a primary key: `post.pk` (also `post.id`).

### Reading

```python
Post.objects.all()                 # every post
Post.objects.get(pk=1)             # exactly one post (error if none or many)
Post.objects.filter(published=True)
```

`Post.objects` is the model's default **manager**, the entry point for database queries. Lesson 7 covers querying in depth.

### Updating

Change attributes and call `save()` again:

```python
post = Post.objects.get(slug="hello")
post.title = "Hello, World"
post.save()
```

To update many rows with a single query:

```python
Post.objects.filter(published=False).update(status="draft")
```

`update()` does not call `save()` and does not trigger signals or refresh `auto_now` fields.

### Deleting

```python
post.delete()                                    # one object
Post.objects.filter(published=False).delete()    # many objects
```

### Refreshing from the database

If the row may have changed elsewhere, reload it:

```python
post.refresh_from_db()
```

### Validation

`save()` does **not** run model validation. To validate manually, call `post.full_clean()`, which raises `ValidationError` if something is invalid. ModelForms (Lesson 10) call it for you.

---

## 5.4 Model methods and Meta

### Custom methods

Add behavior to a model by writing methods. This keeps business logic close to the data:

```python
from django.urls import reverse


class Post(models.Model):
    ...

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse("blog:post_detail", kwargs={"pk": self.pk})

    def is_recent(self):
        from django.utils import timezone
        from datetime import timedelta
        return self.created_at >= timezone.now() - timedelta(days=7)
```

Two methods are especially important:

- `__str__()` controls how an object appears in the shell, the admin, and templates. Always define it.
- `get_absolute_url()` returns the canonical URL of an object. The admin ("View on site" button) and the `redirect()` shortcut use it.

### The Meta class

An inner `Meta` class configures model-level options:

```python
class Post(models.Model):
    ...

    class Meta:
        ordering = ["-created_at"]
        verbose_name = "blog post"
        verbose_name_plural = "blog posts"
        indexes = [
            models.Index(fields=["slug"]),
        ]
        constraints = [
            models.UniqueConstraint(
                fields=["title", "created_at"],
                name="unique_title_per_timestamp",
            ),
        ]
```

| Option | Purpose |
|---|---|
| `ordering` | Default sort order; a leading `-` means descending |
| `verbose_name`, `verbose_name_plural` | Human-readable names used in the admin |
| `db_table` | Override the automatic table name |
| `indexes` | Extra database indexes |
| `constraints` | Database-level rules such as `UniqueConstraint` and `CheckConstraint` |
| `abstract = True` | Makes the model a base class that creates no table of its own |

Prefer `UniqueConstraint` over the older `unique_together` option for multi-field uniqueness.

### Custom managers

When you repeatedly filter the same way, put it in a custom manager:

```python
class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(status="published")


class Post(models.Model):
    ...
    objects = models.Manager()      # keep the default manager
    published_posts = PublishedManager()
```

Now `Post.published_posts.all()` returns only published posts.

### Abstract base models

To share fields across models without repeating them:

```python
class TimeStampedModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True


class Post(TimeStampedModel):
    title = models.CharField(max_length=200)
```

---

[Previous](./[4]-Templates.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[6]-Migrations.md)
