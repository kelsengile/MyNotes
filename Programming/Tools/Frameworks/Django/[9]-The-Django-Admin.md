[Previous](./[8]-Relationships.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[10]-Forms-And-Validation.md)

*Data*

# Lesson 9 - The Django Admin

---

## 9.1 Registering models

The **Django admin** is an automatically generated web interface for managing your data. It reads your models and builds pages to list, create, edit, and delete records. It is meant for trusted staff and developers, not for ordinary visitors of your site.

### Is it enabled?

A new project already includes the admin. You can confirm two things:

```python
# settings.py
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    ...
]
```

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path("admin/", admin.site.urls),
]
```

### Creating an admin account

Apply migrations first (Lesson 6), then create a **superuser**:

```bash
python manage.py migrate
python manage.py createsuperuser
```

Django asks for a username, an optional email, and a password. Start the development server and visit `http://127.0.0.1:8000/admin/` to log in.

### Registering a model

Models do not appear in the admin until you register them in the app's `admin.py`:

```python
# blog/admin.py
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```

Refresh the admin and you will see a **Posts** section with an *Add* button. The admin uses your model's `__str__()` to label each row (Lesson 5), so always define it.

### Registering with a decorator

The decorator form is the most common once you customize the admin (Section 9.2):

```python
from django.contrib import admin
from .models import Post


@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    pass
```

You can register several models at once with `admin.site.register([Author, Tag, Comment])`.

### What the admin gives you for free

- A paginated list view with a bulk **delete** action.
- Add and change forms generated from your field types, including validation.
- A **History** page showing who changed what and when.
- Handling of relationships: foreign keys become drop-downs and many-to-many fields become multi-select widgets.
- Password-protected access with a login page.

### Customizing the labels

Names shown in the admin come from `Meta` (Lesson 5):

```python
class Post(models.Model):
    ...
    class Meta:
        verbose_name = "blog post"
        verbose_name_plural = "blog posts"
```

To rename the whole app section, set `verbose_name` on the `AppConfig` in `apps.py`. To rename the site header, set these in `admin.py` or `urls.py`:

```python
admin.site.site_header = "My Site Administration"
admin.site.site_title = "My Site Admin"
admin.site.index_title = "Dashboard"
```

---

## 9.2 Customizing ModelAdmin

A `ModelAdmin` class controls how one model is displayed and edited.

### Columns in the list page: list_display

By default the list shows one column from `__str__()`. Use `list_display` to show more. Entries can be field names, model methods, or methods on the admin class:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ["title", "author", "status", "created_at", "word_count"]
    list_display_links = ["title"]          # which columns link to the edit page
    list_editable = ["status"]              # edit directly in the list
    ordering = ["-created_at"]

    @admin.display(description="Words", ordering="body")
    def word_count(self, obj):
        return len(obj.body.split())
```

Notes:

- `list_editable` fields must also be in `list_display` and cannot be in `list_display_links`.
- `@admin.display(description=..., ordering=...)` sets the column header and makes it sortable.
- A `ForeignKey` in `list_display` triggers one query per row. Add `list_select_related = ["author"]` to avoid the N+1 problem (Lesson 7).

### Controlling the edit form

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    fields = ["title", "slug", "body", "status"]          # simple: choose and order fields
    readonly_fields = ["created_at", "updated_at"]
    prepopulated_fields = {"slug": ("title",)}            # auto-fill slug while typing
```

For a more organized layout, use `fieldsets`, which group fields under headings:

```python
    fieldsets = [
        ("Content", {"fields": ["title", "slug", "body"]}),
        ("Publishing", {"fields": ["author", "status", "tags"]}),
        ("Metadata", {
            "fields": ["created_at", "updated_at"],
            "classes": ["collapse"],       # collapsed by default
        }),
    ]
```

Use either `fields` or `fieldsets`, not both. Fields with `editable=False` (like `auto_now_add` dates) only appear if you also list them in `readonly_fields`.

### Better widgets for relations

```python
    filter_horizontal = ["tags"]        # dual-list box for many-to-many
    raw_id_fields = ["author"]          # ID input with a lookup popup (for huge tables)
    autocomplete_fields = ["author"]    # search-as-you-type (needs search_fields on Author's admin)
```

### Inlines: edit related objects on the same page

An **inline** lets you edit child records (such as comments) on the parent's page:

```python
class CommentInline(admin.TabularInline):      # or admin.StackedInline
    model = Comment
    extra = 1                                   # number of blank extra rows
    fields = ["name", "text", "approved"]


@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    inlines = [CommentInline]
```

`TabularInline` shows a compact table; `StackedInline` shows each related object as a full form.

### Custom actions

Actions apply an operation to the rows you tick in the list page:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    actions = ["make_published"]

    @admin.action(description="Mark selected posts as published")
    def make_published(self, request, queryset):
        updated = queryset.update(status="published")
        self.message_user(request, f"{updated} posts were marked as published.")
```

### Customizing saving

Override `save_model()` to run code when an object is saved through the admin, for example to record who created it:

```python
    def save_model(self, request, obj, form, change):
        if not change:                      # change is False when creating
            obj.author = request.user
        super().save_model(request, obj, form, change)
```

### Customizing the queryset

```python
    def get_queryset(self, request):
        qs = super().get_queryset(request)
        if request.user.is_superuser:
            return qs
        return qs.filter(author=request.user)       # staff see only their own posts
```

---

## 9.3 Filters and search

### Sidebar filters: list_filter

`list_filter` adds a filter panel on the right side of the list page:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_filter = ["status", "created_at", "author", "tags"]
```

Django chooses a suitable filter for each field type: choices for `BooleanField` and `choices` fields, date ranges ("Today", "Past 7 days", ...) for date fields, and a list of related objects for relations.

You can filter across relationships with double underscores:

```python
    list_filter = ["author__is_active"]
```

### Search box: search_fields

`search_fields` adds a search box that looks for the typed text in the listed fields (a case-insensitive `icontains` match, combined with OR):

```python
    search_fields = ["title", "body", "author__name"]
```

Search prefixes change the lookup type:

| Prefix | Lookup |
|---|---|
| (none) | `icontains` |
| `^` | `istartswith` |
| `=` | `iexact` |
| `@` | Full-text search (PostgreSQL / MySQL) |

```python
    search_fields = ["=slug", "^title", "body"]
```

### Date drill-down: date_hierarchy

```python
    date_hierarchy = "created_at"
```

This adds a navigation bar above the list to drill down by year, month, and day.

### Pagination and list behavior

```python
    list_per_page = 25              # rows per page (default 100)
    list_max_show_all = 200
    save_on_top = True              # show Save buttons at the top of the form too
    empty_value_display = "-"
```

### Custom filters

When the built-in filters are not enough, subclass `SimpleListFilter`:

```python
from django.contrib import admin


class PopularityFilter(admin.SimpleListFilter):
    title = "popularity"
    parameter_name = "popularity"

    def lookups(self, request, model_admin):
        return [("popular", "Popular (100+ views)"), ("new", "Under 100 views")]

    def queryset(self, request, queryset):
        if self.value() == "popular":
            return queryset.filter(views__gte=100)
        if self.value() == "new":
            return queryset.filter(views__lt=100)
        return queryset


class PostAdmin(admin.ModelAdmin):
    list_filter = [PopularityFilter, "status"]
```

### A complete example

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ["title", "author", "status", "created_at"]
    list_filter = ["status", "created_at", "author"]
    search_fields = ["title", "body"]
    date_hierarchy = "created_at"
    prepopulated_fields = {"slug": ("title",)}
    list_select_related = ["author"]
    ordering = ["-created_at"]
```

---

## 9.4 Admin security

The admin gives powerful access to your data, so it is a prime target for attackers. Treat it as sensitive.

### Staff and superusers

Logging into the admin requires `is_staff=True` on the user. A **superuser** (`is_superuser=True`) has every permission automatically. For everyone else, access is controlled by permissions (Lesson 12):

- Each model has four permissions: **add**, **change**, **delete**, and **view**.
- Give staff members only the permissions they need, preferably through **groups**.
- Create very few superusers.

### Customizing permissions for a ModelAdmin

You can override permission checks per model:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    def has_delete_permission(self, request, obj=None):
        return request.user.is_superuser

    def has_add_permission(self, request):
        return request.user.has_perm("blog.add_post")
```

The related methods are `has_view_permission`, `has_change_permission`, `has_add_permission`, and `has_delete_permission`.

### Hardening checklist

- **Use HTTPS** so passwords and session cookies are never sent in clear text. Set `SESSION_COOKIE_SECURE = True` and `CSRF_COOKIE_SECURE = True` in production (Lesson 17).
- **Use strong passwords.** Keep Django's `AUTH_PASSWORD_VALIDATORS` enabled (Lesson 12).
- **Move the admin URL** away from the predictable `/admin/`. This is obscurity, not real security, but it reduces automated attacks:

```python
urlpatterns = [
    path("manage-site-9f3k/", admin.site.urls),
]
```

- **Add two-factor authentication** with a package such as `django-otp` or `django-two-factor-auth`.
- **Rate-limit logins** with a package like `django-axes` to stop brute-force guessing.
- **Restrict by network** where possible, for example allowing `/admin/` only from your office or VPN at the web server level.
- **Never run with `DEBUG = True`** in production (Lesson 17).
- **Keep Django updated** so security fixes are applied.

### Be careful with the admin as a feature

- Do not give the admin to the general public. It is designed for trusted staff, and its interface is not meant to be an end-user product.
- Be cautious with `|safe` or `mark_safe` on user-submitted data shown in custom admin columns, since it disables HTML escaping and can open an XSS hole.
- Admin actions with `queryset.delete()` do not call each model's `delete()` method or per-object signals, so use caution when you rely on those.

### Auditing

The admin's **History** page records changes made through the admin for each object, and `django.contrib.admin.models.LogEntry` stores them in the database. For stronger auditing, use a package like `django-simple-history`.

---

[Previous](./[8]-Relationships.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[10]-Forms-And-Validation.md)
