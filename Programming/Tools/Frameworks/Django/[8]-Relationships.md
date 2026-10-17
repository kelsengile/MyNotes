[Previous](./[7]-Querying-With-QuerySets.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[9]-The-Django-Admin.md)

*Data*

# Lesson 8 - Relationships

---

## 8.1 One-to-many

Real applications rarely have isolated tables. A blog has authors **and** posts **and** comments, and they are connected. Django models these connections with three relationship fields: `ForeignKey`, `ManyToManyField`, and `OneToOneField`.

### ForeignKey

A **one-to-many** relationship means one object on the "one" side can be linked to many objects on the "many" side. One author writes many posts; each post has one author. You declare it with a `ForeignKey` on the **"many"** side:

```python
# blog/models.py
from django.db import models


class Author(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name


class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

    def __str__(self):
        return self.title
```

Behind the scenes Django adds a column named `author_id` to the `blog_post` table that stores the author's primary key, plus a database foreign-key constraint.

### on_delete is required

`on_delete` tells Django what to do with a post when its author is deleted:

| Option | Behavior |
|---|---|
| `models.CASCADE` | Delete the posts too |
| `models.PROTECT` | Block the deletion by raising `ProtectedError` |
| `models.RESTRICT` | Like `PROTECT`, but allows deletion if the object is also being removed via another cascade |
| `models.SET_NULL` | Set the field to `NULL` (requires `null=True`) |
| `models.SET_DEFAULT` | Set the field to its default value (requires `default=`) |
| `models.SET(value)` | Set the field to a given value or callable result |
| `models.DO_NOTHING` | Do nothing; may cause database integrity errors |

Pick deliberately. For example, comments usually `CASCADE` with their post, while a post's author is often `PROTECT` or `SET_NULL` so content is not lost by accident.

```python
author = models.ForeignKey(
    Author,
    on_delete=models.SET_NULL,
    null=True,
    blank=True,
)
```

### Using a ForeignKey

```python
ada = Author.objects.create(name="Ada")

post = Post.objects.create(title="Hello", author=ada)

post.author            # <Author: Ada>
post.author.name       # "Ada"
post.author_id         # 1, the raw key, available without loading the Author
```

You can also assign by primary key: `Post.objects.create(title="Hi", author_id=1)`.

### Referring to models in other apps or later in the file

Pass the model as a string when it is defined later in the file, or lives in another app:

```python
author = models.ForeignKey("Author", on_delete=models.CASCADE)            # same app
author = models.ForeignKey("accounts.Profile", on_delete=models.CASCADE)  # another app
```

For the user model always use `settings.AUTH_USER_MODEL` (see Lesson 12):

```python
from django.conf import settings

author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```

### Self-referential relationships

A model can point to itself, such as threaded comments or categories with parents:

```python
class Category(models.Model):
    name = models.CharField(max_length=100)
    parent = models.ForeignKey(
        "self", on_delete=models.CASCADE, null=True, blank=True, related_name="children"
    )
```

### Useful ForeignKey options

| Option | Purpose |
|---|---|
| `null=True`, `blank=True` | Makes the relation optional |
| `related_name="..."` | Name for the reverse accessor (Section 8.4) |
| `limit_choices_to={...}` | Restricts the choices shown in forms and the admin |
| `db_constraint=False` | Skips the database-level constraint (rarely needed) |
| `to_field="..."` | Points at a unique field other than the primary key |

---

## 8.2 Many-to-many

### ManyToManyField

A **many-to-many** relationship means each object on either side can be linked to many on the other. A post can have many tags, and a tag can belong to many posts. Declare it on **either** one of the two models (not both):

```python
class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)

    def __str__(self):
        return self.name


class Post(models.Model):
    title = models.CharField(max_length=200)
    tags = models.ManyToManyField(Tag, blank=True)
```

Django creates a hidden **join table** (here `blog_post_tags`) with one row per link. You never touch it directly.

### Working with a many-to-many relation

A many-to-many field gives you a **related manager**. Both objects must be saved first:

```python
post = Post.objects.create(title="Hello")
python = Tag.objects.create(name="python")
django = Tag.objects.create(name="django")

post.tags.add(python, django)        # link
post.tags.remove(python)             # unlink one
post.tags.set([django, python])      # replace all links with exactly this list
post.tags.clear()                    # remove all links
post.tags.create(name="web")         # create a Tag and link it in one step

post.tags.all()                      # QuerySet of Tag objects
post.tags.filter(name__startswith="dj")
post.tags.count()
```

You can also use primary keys: `post.tags.add(1, 2)`.

### The reverse direction

From a `Tag` you reach its posts through the automatic `post_set` manager (or your `related_name`):

```python
python.post_set.all()
```

### Querying across a many-to-many

```python
Post.objects.filter(tags__name="python")
Post.objects.filter(tags__name__in=["python", "django"]).distinct()
Tag.objects.filter(post__title__icontains="hello")
```

Use `.distinct()` when a filter could match the same object through several links.

### Extra data on the link: the through model

Sometimes the relationship itself has data, such as **when** a user joined a group or what **role** they have. Define your own join model and point to it with `through`:

```python
class Group(models.Model):
    name = models.CharField(max_length=100)
    members = models.ManyToManyField("auth.User", through="Membership")


class Membership(models.Model):
    user = models.ForeignKey("auth.User", on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    role = models.CharField(max_length=20, default="member")
    joined_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        constraints = [
            models.UniqueConstraint(fields=["user", "group"], name="unique_membership"),
        ]
```

Create links by creating `Membership` objects directly:

```python
Membership.objects.create(user=user, group=group, role="admin")
```

On modern Django versions `add()`, `set()`, and `create()` also work on `through` relations if you pass `through_defaults={"role": "admin"}`.

### Symmetrical self relationships

A many-to-many to `"self"` is **symmetrical** by default: if A is a friend of B, B is a friend of A. For one-way relations such as "follows", turn that off:

```python
class Profile(models.Model):
    following = models.ManyToManyField("self", symmetrical=False, related_name="followers")
```

---

## 8.3 One-to-one

### OneToOneField

A **one-to-one** relationship links each object to at most one object on the other side. It behaves like a `ForeignKey` with `unique=True`. Its classic use is extending a model you cannot (or do not want to) change, such as adding a profile to each user:

```python
from django.conf import settings
from django.db import models


class Profile(models.Model):
    user = models.OneToOneField(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
        related_name="profile",
    )
    bio = models.TextField(blank=True)
    birth_date = models.DateField(null=True, blank=True)

    def __str__(self):
        return f"Profile of {self.user}"
```

### Using it

```python
profile = Profile.objects.create(user=user, bio="Python developer")

profile.user            # the User
user.profile            # the Profile (the reverse accessor is a single object, not a manager)
user.profile.bio
```

Because the reverse side is a single object, accessing it when none exists raises `Profile.DoesNotExist` (technically `RelatedObjectDoesNotExist`, a subclass). Guard against it:

```python
try:
    bio = user.profile.bio
except Profile.DoesNotExist:
    bio = ""

hasattr(user, "profile")     # a quick existence check
```

### Creating the profile automatically

A common pattern is to create a profile whenever a user is created, using a signal (Lesson 13):

```python
from django.db.models.signals import post_save
from django.dispatch import receiver


@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```

### Multi-table inheritance

Django also uses a one-to-one link internally for **multi-table inheritance**, where a child model inherits from a concrete parent model:

```python
class Place(models.Model):
    name = models.CharField(max_length=100)


class Restaurant(Place):
    serves_pizza = models.BooleanField(default=False)
```

Django creates a table for each model and a hidden `place_ptr` one-to-one field linking them. It works, but every query on a `Restaurant` joins two tables, so prefer abstract base models (Lesson 5) unless you truly need to query the parent separately.

### Choosing the right relationship

| Situation | Field |
|---|---|
| Each post has one author, an author has many posts | `ForeignKey` on `Post` |
| Posts have many tags, tags have many posts | `ManyToManyField` |
| Each user has exactly one profile | `OneToOneField` |
| The link carries its own data (role, date joined) | `ManyToManyField` with `through` |

---

## 8.4 Related names and reverse lookups

### The reverse accessor

Every relationship works in **both** directions. When you define a `ForeignKey` on `Post` pointing to `Author`, Django automatically gives each `Author` a way to reach its posts.

By default the reverse accessor is named `<lowercase model name>_set`:

```python
ada.post_set.all()                       # all posts by Ada
ada.post_set.filter(title__icontains="django")
ada.post_set.create(title="New post")    # creates a post with author=ada
ada.post_set.count()
```

### related_name

The default `post_set` is awkward. Set a clearer name with `related_name`:

```python
class Post(models.Model):
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="posts")
```

```python
ada.posts.all()
```

Use **plural** names for the "many" side (`posts`, `comments`) and **singular** names for one-to-one (`profile`).

### related_name is required when there are two links to the same model

If a model has two relations to the same target, the default reverse names would collide, so Django raises an error until you give each its own `related_name`:

```python
class Message(models.Model):
    sender = models.ForeignKey("auth.User", on_delete=models.CASCADE, related_name="sent_messages")
    recipient = models.ForeignKey("auth.User", on_delete=models.CASCADE, related_name="received_messages")
```

### Hiding the reverse relation

Set `related_name="+"` if you never need to go backward:

```python
created_by = models.ForeignKey("auth.User", on_delete=models.SET_NULL, null=True, related_name="+")
```

### related_query_name: the name used in filters

Reverse **filters** use the model name by default (`Author.objects.filter(post__title=...)`), while the accessor uses `related_name`. When you set `related_name`, the query name changes with it. To control the query name separately, use `related_query_name`:

```python
author = models.ForeignKey(
    Author, on_delete=models.CASCADE,
    related_name="posts", related_query_name="post",
)
```

```python
Author.objects.filter(post__title__icontains="django")
```

### Reverse lookups in queries

```python
Author.objects.filter(posts__status="published").distinct()
Author.objects.annotate(post_count=Count("posts")).filter(post_count__gt=3)
Author.objects.filter(posts__isnull=True)       # authors with no posts
```

### Reverse relations and performance

Reverse accessors load their data lazily. Use `prefetch_related` (Lesson 7) to avoid the N+1 problem:

```python
for author in Author.objects.prefetch_related("posts"):
    print(author.name, [p.title for p in author.posts.all()])
```

### Reversing a ForeignKey relation: key points

- The related manager (`ada.posts`) behaves like a normal manager, so `.all()`, `.filter()`, `.create()`, and the rest work.
- Assigning with `set()` on a reverse `ForeignKey` manager only works if the `ForeignKey` is nullable.
- Deleting an object follows the `on_delete` rule of each relation that points at it.

### Summary of accessors

| Relation | Forward | Reverse |
|---|---|---|
| `ForeignKey` | `post.author` (an object) | `author.posts` (a manager) |
| `ManyToManyField` | `post.tags` (a manager) | `tag.post_set` or `related_name` (a manager) |
| `OneToOneField` | `profile.user` (an object) | `user.profile` (an object) |

---

[Previous](./[7]-Querying-With-QuerySets.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[9]-The-Django-Admin.md)
