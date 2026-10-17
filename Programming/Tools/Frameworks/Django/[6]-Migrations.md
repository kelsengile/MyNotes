[Previous](./[5]-Models-And-The-ORM.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[7]-Querying-With-QuerySets.md)

*Data*

# Lesson 6 - Migrations

---

## 6.1 How migrations work

A **migration** is a Python file that describes a change to your database schema: creating a table, adding a column, renaming a field, and so on. Migrations are essentially **version control for your database**. They let you evolve your models over time and apply the same changes consistently on every machine (your laptop, a teammate's laptop, and the production server).

### The workflow

1. You change a model in `models.py`.
2. You run `makemigrations`, which compares your models to the existing migration history and writes a new migration file.
3. You run `migrate`, which executes the migration against the database.

Django never changes your database schema automatically when you edit a model. You must go through these two commands.

### Where migrations live

Each app has a `migrations/` folder:

```
blog/
└── migrations/
    ├── __init__.py
    ├── 0001_initial.py
    └── 0002_post_status.py
```

Files are numbered in order. Each migration declares which migrations it depends on, forming a chain.

### Anatomy of a migration file

```python
# blog/migrations/0001_initial.py
from django.db import migrations, models


class Migration(migrations.Migration):

    initial = True

    dependencies = []

    operations = [
        migrations.CreateModel(
            name="Post",
            fields=[
                ("id", models.BigAutoField(primary_key=True, serialize=False)),
                ("title", models.CharField(max_length=200)),
                ("body", models.TextField()),
            ],
        ),
    ]
```

| Part | Meaning |
|---|---|
| `dependencies` | Migrations that must be applied before this one |
| `operations` | The list of schema changes (`CreateModel`, `AddField`, `AlterField`, `RenameField`, `DeleteModel`, `RunPython`, ...) |

### How Django tracks what has been applied

Django stores the names of applied migrations in a special table called `django_migrations`. When you run `migrate`, it compares that table to the migration files on disk and applies only the ones that are missing.

Even built-in apps such as `auth`, `admin`, and `sessions` have migrations. That is why the first `migrate` creates a number of tables before you have written any models.

---

## 6.2 Creating and applying migrations

### makemigrations

```bash
python manage.py makemigrations            # all apps
python manage.py makemigrations blog       # one app
python manage.py makemigrations blog --name add_post_status
```

Example output:

```
Migrations for 'blog':
  blog/migrations/0002_post_status.py
    + Add field status to post
```

Always **read the generated file** before applying it, especially after renames, since Django may interpret a rename as "delete a column and add a new one", which would lose data. When Django is unsure, it asks interactively whether you renamed a field.

### migrate

```bash
python manage.py migrate                   # apply everything pending
python manage.py migrate blog              # apply for one app
python manage.py migrate blog 0001         # migrate to a specific point
```

### Inspecting migrations

```bash
python manage.py showmigrations            # list migrations and which are applied [X]
python manage.py sqlmigrate blog 0002      # print the SQL a migration would run
```

`sqlmigrate` is a great way to see exactly what Django will do to the database without running it.

### Adding a required field to an existing table

If a table already has rows and you add a non-null field with no default, Django cannot fill in the existing rows. It will prompt you:

```
It is impossible to add a non-nullable field 'status' without specifying a default...
```

Solve it by giving the field a `default`, making it `null=True`, or choosing the one-off default Django offers during `makemigrations`.

### Best practices

- **Commit migration files** to version control along with your model changes. Teammates and servers need them.
- **Never edit or delete a migration** that has already been applied in a shared or production database. Create a new migration instead.
- Run `makemigrations` often, in small steps, so each migration stays simple.
- Run `python manage.py makemigrations --check` in CI to confirm no model changes are missing a migration.
- If two developers create migrations with the same number, Django reports a conflict. Fix it with `python manage.py makemigrations --merge`.

---

## 6.3 Reversing and squashing

### Reversing a migration

You can roll the database back to an earlier state by migrating to an earlier migration name or number:

```bash
python manage.py migrate blog 0001     # undo everything after 0001
python manage.py migrate blog zero     # undo ALL migrations for the app
```

Reversal works because most operations know how to undo themselves (`CreateModel` reverses to dropping the table, `AddField` to removing the column). Be careful: reversing a migration that deletes or adds columns may **destroy data**. Always back up important data before reversing on a real database.

Some operations cannot be reversed automatically, such as a `RunPython` without a reverse function. Those raise an `IrreversibleError`.

### Squashing migrations

Over a long project's life you may accumulate dozens or hundreds of migrations. **Squashing** combines a range of them into one optimized migration:

```bash
python manage.py squashmigrations blog 0001 0010
```

This creates a new file such as `0001_squashed_0010_...py` with a `replaces` list. Django uses the squashed version for new databases and the originals for databases that have already applied them. Once every environment has applied the migrations, you can delete the old files and remove the `replaces` line.

### Faking migrations

Occasionally the database already matches a migration (for example, you created the table by hand). You can tell Django to record the migration as applied **without running it**:

```bash
python manage.py migrate blog 0001 --fake
```

Use this only when you fully understand the state of your database.

---

## 6.4 Data migrations

Schema migrations change the **structure** of the database. A **data migration** changes the **data** inside it, such as filling a new column based on old values or loading initial records.

### Creating an empty migration

```bash
python manage.py makemigrations blog --empty --name populate_status
```

### Writing a RunPython operation

```python
from django.db import migrations


def set_default_status(apps, schema_editor):
    Post = apps.get_model("blog", "Post")
    Post.objects.filter(published=True).update(status="published")
    Post.objects.filter(published=False).update(status="draft")


def reverse_status(apps, schema_editor):
    Post = apps.get_model("blog", "Post")
    Post.objects.update(status="draft")


class Migration(migrations.Migration):

    dependencies = [
        ("blog", "0002_post_status"),
    ]

    operations = [
        migrations.RunPython(set_default_status, reverse_status),
    ]
```

Key points:

- **Use `apps.get_model()`**, not a direct import of your model. The historical model reflects the schema *at that point in migration history*, which may differ from your current `models.py`.
- Provide a **reverse function** as the second argument so the migration can be undone. If there is nothing to undo, use `migrations.RunPython.noop`:

```python
migrations.RunPython(set_default_status, migrations.RunPython.noop)
```

- Custom methods on your model are **not** available on historical models. Only fields and default managers are.

### Combining schema and data changes safely

When making a risky change, split it into steps across separate migrations. For example, to add a required field to a table with data:

1. Add the field as `null=True`.
2. Run a data migration to fill in values.
3. Alter the field to `null=False`.

This approach avoids failures on existing rows and keeps each step reversible.

### Raw SQL

For rare cases, `migrations.RunSQL("...", reverse_sql="...")` lets you execute raw SQL, but prefer built-in operations whenever possible because they work across all supported database backends.

---

[Previous](./[5]-Models-And-The-ORM.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[7]-Querying-With-QuerySets.md)
