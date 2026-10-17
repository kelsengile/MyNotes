[Previous](./[6]-Migrations.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[8]-Relationships.md)

*Data*

# Lesson 7 - Querying With QuerySets

---

## 7.1 Filtering and ordering

A **QuerySet** represents a collection of rows from your database. You get one by calling a method on a model's manager (`Post.objects`). QuerySets are how you read data with the ORM.

The examples in this lesson use this model (relations are explained in Lesson 8):

```python
# blog/models.py
from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    body = models.TextField()
    status = models.CharField(max_length=10, default="draft")
    views = models.PositiveIntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

### QuerySets are lazy

Building a QuerySet does **not** hit the database. Django only runs the SQL when you actually need the results: when you loop over it, slice it with a step, call `list()`, `len()`, or `bool()` on it, or print it in the shell.

```python
posts = Post.objects.filter(status="published")   # no query yet
for post in posts:                                # query runs here
    print(post.title)
```

Once a QuerySet is evaluated, its results are **cached** on that object, so looping over it a second time does not query again.

### Retrieving objects

| Method | Returns | Notes |
|---|---|---|
| `all()` | A QuerySet of every row | |
| `filter(**kwargs)` | A QuerySet of rows that match | May be empty |
| `exclude(**kwargs)` | A QuerySet of rows that do **not** match | |
| `get(**kwargs)` | A single object | Raises `DoesNotExist` if none, `MultipleObjectsReturned` if many |
| `first()` / `last()` | A single object or `None` | Uses the ordering (or primary key if none) |
| `count()` | An integer | Runs `SELECT COUNT(*)`, faster than `len()` |
| `exists()` | `True` / `False` | Cheapest way to check for any match |

```python
Post.objects.filter(status="published")
Post.objects.exclude(status="archived")

try:
    post = Post.objects.get(slug="hello")
except Post.DoesNotExist:
    post = None
```

### Chaining filters

Each filter method returns a **new** QuerySet, so you can chain them. Conditions in a chain are combined with **AND**:

```python
Post.objects.filter(status="published").exclude(views=0).filter(title__startswith="D")
```

The original QuerySet is never modified; every chain step produces a fresh copy.

### Ordering

Use `order_by()`. A leading `-` means descending. Passing several fields breaks ties in order:

```python
Post.objects.order_by("title")                 # A to Z
Post.objects.order_by("-created_at")           # newest first
Post.objects.order_by("-views", "title")       # most views, then alphabetical
Post.objects.order_by("?")                     # random (slow on big tables)
```

If you do not call `order_by()`, Django uses the model's `Meta.ordering` (Lesson 5), and if that is empty the order is **not guaranteed**.

### Slicing and limiting

QuerySets support Python slice syntax, which becomes `LIMIT` and `OFFSET` in SQL:

```python
Post.objects.order_by("-created_at")[:5]       # first 5
Post.objects.order_by("-created_at")[5:10]     # the next 5
Post.objects.order_by("-created_at")[0]        # a single object
```

Negative indexes are **not** supported.

### Getting dictionaries or tuples instead of objects

When you only need a few columns, `values()` and `values_list()` are lighter than building full model instances:

```python
Post.objects.values("id", "title")
# <QuerySet [{'id': 1, 'title': 'Hello'}, {'id': 2, 'title': 'Second'}]>

Post.objects.values_list("title", flat=True)
# <QuerySet ['Hello', 'Second']>
```

`flat=True` is only allowed when you request exactly one field.

### Removing duplicates

```python
Post.objects.values_list("status", flat=True).distinct()
```

### Inspecting the generated SQL

```python
qs = Post.objects.filter(status="published")
print(qs.query)
```

---

## 7.2 Lookups and Q objects

### Field lookups

A **lookup** is the double-underscore suffix you add to a field name inside `filter()`, `exclude()`, or `get()`. The general form is `field__lookuptype=value`. If you leave the lookup off, Django assumes `exact`.

| Lookup | Meaning | Example |
|---|---|---|
| `exact` | Equal to | `title__exact="Hello"` |
| `iexact` | Equal, ignoring case | `title__iexact="hello"` |
| `contains` | Contains substring (case-sensitive) | `body__contains="Django"` |
| `icontains` | Contains substring, ignoring case | `body__icontains="django"` |
| `startswith`, `endswith` | Begins or ends with | `title__startswith="How"` |
| `in` | Value is in a list | `status__in=["draft", "published"]` |
| `gt`, `gte` | Greater than (or equal) | `views__gte=100` |
| `lt`, `lte` | Less than (or equal) | `views__lt=10` |
| `range` | Between two values (inclusive) | `views__range=(10, 100)` |
| `isnull` | Is `NULL` or not | `published_at__isnull=True` |
| `regex`, `iregex` | Regular-expression match | `title__regex=r"^\d+"` |

Date and datetime fields also support part-based lookups:

```python
Post.objects.filter(created_at__year=2026)
Post.objects.filter(created_at__month=3)
Post.objects.filter(created_at__date=date(2026, 3, 14))
Post.objects.filter(created_at__gte=timezone.now() - timedelta(days=7))
```

### Case sensitivity note

On SQLite, `contains` is effectively case-insensitive for ASCII text, but on PostgreSQL it is case-sensitive. Use `icontains` when you want case-insensitive behavior everywhere.

### Q objects: OR, NOT, and complex logic

Keyword arguments in `filter()` are always joined with AND. To use **OR** or **NOT**, wrap conditions in `Q` objects:

```python
from django.db.models import Q

# OR
Post.objects.filter(Q(title__icontains="django") | Q(body__icontains="django"))

# AND (same as normal keyword arguments)
Post.objects.filter(Q(status="published") & Q(views__gt=100))

# NOT
Post.objects.filter(~Q(status="archived"))

# Combined
Post.objects.filter(
    Q(status="published") & (Q(views__gt=100) | Q(title__startswith="Intro"))
)
```

You can mix `Q` objects and keyword arguments, but the `Q` objects must come **first**:

```python
Post.objects.filter(Q(title__icontains="django") | Q(body__icontains="django"), status="published")
```

### Building queries dynamically

`Q` objects are ideal for search forms where some filters are optional:

```python
def search(request):
    query = request.GET.get("q", "")
    status = request.GET.get("status", "")

    filters = Q()
    if query:
        filters &= Q(title__icontains=query) | Q(body__icontains=query)
    if status:
        filters &= Q(status=status)

    posts = Post.objects.filter(filters)
```

### F expressions: comparing a field to another field

An **F expression** refers to the value of a column, so the comparison or update happens inside the database rather than in Python:

```python
from django.db.models import F

# Posts where the last-updated count exceeds views
Post.objects.filter(comment_count__gt=F("views"))

# Atomic increment: no race conditions
Post.objects.filter(pk=1).update(views=F("views") + 1)
```

The increment above is safer than reading `post.views`, adding one, and saving, because two simultaneous requests cannot overwrite each other.

---

## 7.3 Aggregation and annotation

### aggregate(): one summary value for the whole QuerySet

`aggregate()` collapses a QuerySet into a **dictionary** of summary values:

```python
from django.db.models import Avg, Count, Max, Min, Sum

Post.objects.aggregate(Avg("views"))
# {'views__avg': 42.5}

Post.objects.aggregate(total=Sum("views"), most=Max("views"), fewest=Min("views"))
# {'total': 850, 'most': 300, 'fewest': 0}

Post.objects.filter(status="published").aggregate(n=Count("id"))
```

Common aggregate functions: `Count`, `Sum`, `Avg`, `Min`, `Max`, plus `StdDev` and `Variance`.

### annotate(): add a computed value to every object

`annotate()` attaches an extra attribute to **each** object in the QuerySet. It is most often used with relations (Lesson 8). Assuming a `Comment` model with a foreign key to `Post` and `related_name="comments"`:

```python
posts = Post.objects.annotate(comment_total=Count("comments"))

for post in posts:
    print(post.title, post.comment_total)
```

You can then filter and order on the annotation as if it were a normal field:

```python
Post.objects.annotate(comment_total=Count("comments")).filter(comment_total__gte=5)
Post.objects.annotate(comment_total=Count("comments")).order_by("-comment_total")
```

### Grouping with values() and annotate()

Calling `values()` **before** `annotate()` groups the rows by that field (like SQL `GROUP BY`):

```python
Post.objects.values("status").annotate(n=Count("id"))
# <QuerySet [{'status': 'draft', 'n': 4}, {'status': 'published', 'n': 9}]>
```

### Aggregating over annotations

```python
from django.db.models import Avg

Post.objects.annotate(c=Count("comments")).aggregate(average=Avg("c"))
```

### Conditional counts with filter

Pass `filter=` to an aggregate to count or sum only some related rows:

```python
Post.objects.annotate(
    approved_comments=Count("comments", filter=Q(comments__approved=True))
)
```

### Computed fields with expressions

You can annotate with arithmetic, functions, and conditionals:

```python
from django.db.models import Case, CharField, F, Value, When
from django.db.models.functions import Length, Lower

Post.objects.annotate(title_length=Length("title"))

Post.objects.annotate(
    popularity=Case(
        When(views__gte=1000, then=Value("hot")),
        When(views__gte=100, then=Value("warm")),
        default=Value("cold"),
        output_field=CharField(),
    )
)
```

### Order matters: filter before or after annotate

A `filter()` placed **before** `annotate()` restricts which rows are counted; a `filter()` placed **after** filters on the computed value:

```python
# Count only approved comments, then keep posts that have any
Post.objects.filter(comments__approved=True).annotate(n=Count("comments"))

# Count all comments, then keep posts with 5 or more
Post.objects.annotate(n=Count("comments")).filter(n__gte=5)
```

---

## 7.4 Relations and select_related

### Following relations with double underscores

Use `__` to cross a relationship in a filter. Assuming `Post` has a `ForeignKey` named `author` to an `Author` model (Lesson 8):

```python
Post.objects.filter(author__name="Ada")
Post.objects.filter(author__name__icontains="ad")
Author.objects.filter(post__status="published")      # reverse direction
```

### The N+1 query problem

By default, Django loads related objects **lazily**, one query at a time. This code looks innocent but is slow:

```python
posts = Post.objects.all()          # 1 query
for post in posts:
    print(post.author.name)         # 1 extra query PER post
```

With 100 posts that is 101 queries. This is called the **N+1 problem** and it is one of the most common performance mistakes in Django.

### select_related(): for single-valued relations

`select_related()` follows `ForeignKey` and `OneToOneField` relations using a SQL **JOIN**, fetching everything in **one** query:

```python
posts = Post.objects.select_related("author")      # 1 query total
for post in posts:
    print(post.author.name)                        # no extra query

# Follow several levels or several relations
Post.objects.select_related("author", "category")
Comment.objects.select_related("post__author")
```

### prefetch_related(): for many-valued relations

`ManyToManyField` and reverse `ForeignKey` relations return many objects, so a JOIN would duplicate rows. `prefetch_related()` instead runs **one extra query per relation** and joins the results in Python:

```python
posts = Post.objects.prefetch_related("comments", "tags")   # 3 queries total

for post in posts:
    for comment in post.comments.all():     # uses the prefetched data
        print(comment.text)
```

To customize the prefetched rows, use a `Prefetch` object:

```python
from django.db.models import Prefetch

approved = Comment.objects.filter(approved=True).order_by("-created_at")
Post.objects.prefetch_related(Prefetch("comments", queryset=approved, to_attr="approved_comments"))
```

### Which one should I use?

| Relation type | Use |
|---|---|
| `ForeignKey` (forward) | `select_related` |
| `OneToOneField` (either direction) | `select_related` |
| `ManyToManyField` | `prefetch_related` |
| Reverse `ForeignKey` (`post.comments`) | `prefetch_related` |

### Loading fewer columns: only() and defer()

```python
Post.objects.only("title", "slug")      # load just these columns
Post.objects.defer("body")              # load everything except body
```

Accessing a deferred field later triggers an extra query for each object, so use these only when you are sure you will not need the skipped columns.

### Measuring queries

To see how many queries a page really makes:

```python
from django.db import connection, reset_queries
from django.conf import settings

settings.DEBUG = True       # query logging only works with DEBUG = True
reset_queries()
list(Post.objects.select_related("author"))
print(len(connection.queries))
```

The Django Debug Toolbar package shows the same information in the browser during development.

### Useful extras

- `bulk_create([...])` inserts many objects in one query.
- `bulk_update(objs, ["field"])` updates many objects in one query.
- `get_or_create(...)` and `update_or_create(...)` fetch an object or create it, and return `(object, created)`.
- `Post.objects.filter(...).delete()` removes many rows in one query.

```python
post, created = Post.objects.get_or_create(
    slug="hello",
    defaults={"title": "Hello", "body": "First post"},
)
```

---

[Previous](./[6]-Migrations.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[8]-Relationships.md)
