[Previous](./[10]-Forms-And-Validation.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[12]-Authentication-And-Permissions.md)

*Building Features*

# Lesson 11 - Class-Based Views

---

## 11.1 Generic views

In Lesson 3 you wrote views as plain functions. Django also lets you write a view as a **class**. The big advantage is reuse: Django ships with **generic views** that implement common patterns (list some objects, show one object, handle a form) so you only supply the details.

### A basic class-based view

A class-based view (CBV) inherits from `django.views.View` and defines one method per HTTP method:

```python
# blog/views.py
from django.http import HttpResponse
from django.views import View


class HelloView(View):
    def get(self, request):
        return HttpResponse("Hello from a class!")

    def post(self, request):
        return HttpResponse("You sent a POST.")
```

A request using a method with no matching handler receives `405 Method Not Allowed` automatically.

### Connecting a CBV to a URL

URLs need a **function**, so call the class method `as_view()`:

```python
# blog/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path("hello/", views.HelloView.as_view(), name="hello"),
]
```

`as_view()` returns a function that creates a fresh instance of your class for every request and then calls its `dispatch()` method, which picks `get()`, `post()`, and so on based on `request.method`.

URL arguments arrive as `self.kwargs` (and extra positional ones as `self.args`). The request is `self.request`. Both are available in any method of the class:

```python
class GreetView(View):
    def get(self, request, name):
        return HttpResponse(f"Hello, {name}!")

# path("greet/<str:name>/", GreetView.as_view())
```

### Passing options in as_view()

Class attributes can be overridden per URL:

```python
class PageView(View):
    message = "Default"

    def get(self, request):
        return HttpResponse(self.message)

urlpatterns = [
    path("a/", PageView.as_view(message="Page A")),
    path("b/", PageView.as_view(message="Page B")),
]
```

Only attributes that already exist on the class can be passed this way.

### TemplateView and RedirectView

Two of the simplest generic views:

```python
from django.views.generic import RedirectView, TemplateView


class AboutView(TemplateView):
    template_name = "blog/about.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["team_size"] = 12
        return context
```

```python
urlpatterns = [
    path("about/", AboutView.as_view(), name="about"),
    path("old/", RedirectView.as_view(pattern_name="blog:post_list", permanent=True)),
    # a TemplateView needs no subclass for a static page:
    path("terms/", TemplateView.as_view(template_name="blog/terms.html")),
]
```

### Function-based or class-based?

| | Function-based views | Class-based views |
|---|---|---|
| Learning curve | Easy | Steeper at first |
| Behavior | Fully explicit | Inherited from parent classes |
| Reuse | Via helper functions and decorators | Via inheritance and mixins |
| Best for | Unusual, one-off logic | Standard CRUD pages and shared behavior |

Neither is "better". Use CBVs when a generic view fits and plain functions when your logic is unusual and a CBV would obscure it.

### Useful hooks

Most generic views are customized by overriding a small number of methods:

| Method | Purpose |
|---|---|
| `dispatch(request, ...)` | Runs first; routes to `get()` / `post()` |
| `get_context_data(**kwargs)` | Builds the template context |
| `get_queryset()` | Chooses which objects to work with |
| `get_object()` | Chooses the single object to work with |
| `get_template_names()` | Chooses the template |
| `form_valid(form)` / `form_invalid(form)` | Runs when a form is valid or invalid |
| `get_success_url()` | Chooses where to redirect after success |

### Decorators on class-based views

Function decorators like `login_required` must be wrapped with `method_decorator`:

```python
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator


@method_decorator(login_required, name="dispatch")
class DashboardView(TemplateView):
    template_name = "blog/dashboard.html"
```

In practice you will usually prefer a **mixin** such as `LoginRequiredMixin` (Section 11.4).

---

## 11.2 ListView and DetailView

These two views cover the most common pages on any site: a list of things and a page for one thing.

### ListView

```python
# blog/views.py
from django.views.generic import ListView
from .models import Post


class PostListView(ListView):
    model = Post
```

With just `model = Post`, Django assumes:

- The queryset is `Post.objects.all()`.
- The template is `blog/post_list.html` (`<app>/<model>_list.html`).
- The context variable is `object_list`, and also `post_list` (`<model>_list`).

Customize with attributes:

```python
class PostListView(ListView):
    model = Post
    template_name = "blog/post_list.html"    # override the default template name
    context_object_name = "posts"            # the name used in the template
    paginate_by = 10                         # 10 objects per page
    ordering = ["-created_at"]
```

Template:

```html
{% extends "base.html" %}

{% block content %}
  <h1>Posts</h1>
  {% for post in posts %}
    <h2><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></h2>
  {% empty %}
    <p>No posts yet.</p>
  {% endfor %}

  {% if is_paginated %}
    <nav>
      {% if page_obj.has_previous %}
        <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
      {% endif %}
      Page {{ page_obj.number }} of {{ paginator.num_pages }}
      {% if page_obj.has_next %}
        <a href="?page={{ page_obj.next_page_number }}">Next</a>
      {% endif %}
    </nav>
  {% endif %}
{% endblock %}
```

When `paginate_by` is set, the context also contains `is_paginated`, `page_obj`, and `paginator`.

### Customizing the queryset

Override `get_queryset()` for filtering that depends on the request:

```python
class PublishedPostListView(ListView):
    model = Post
    context_object_name = "posts"
    paginate_by = 10

    def get_queryset(self):
        qs = Post.objects.filter(status="published").select_related("author")
        query = self.request.GET.get("q")
        if query:
            qs = qs.filter(title__icontains=query)
        return qs
```

### DetailView

```python
from django.views.generic import DetailView


class PostDetailView(DetailView):
    model = Post
```

Defaults:

- The object is looked up by the URL keyword argument named `pk` (or `slug`).
- The template is `blog/post_detail.html` (`<app>/<model>_detail.html`).
- The context variable is `object`, and also `post` (the lowercase model name).
- A missing object produces a 404 automatically, like `get_object_or_404()`.

URLs:

```python
urlpatterns = [
    path("", views.PostListView.as_view(), name="post_list"),
    path("<int:pk>/", views.PostDetailView.as_view(), name="post_detail"),
    path("<slug:slug>/", views.PostDetailView.as_view(), name="post_detail_slug"),
]
```

To look up by slug, the URL must capture a keyword named `slug` and the model needs a `slug` field (or set `slug_field` and `slug_url_kwarg` to other names).

### Adding extra context

```python
class PostDetailView(DetailView):
    model = Post

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["related_posts"] = Post.objects.exclude(pk=self.object.pk)[:3]
        return context
```

Always call `super().get_context_data(**kwargs)` first so the standard context is preserved. In `DetailView`, the current object is `self.object`.

### Restricting what can be viewed

```python
class PostDetailView(DetailView):
    model = Post

    def get_queryset(self):
        return Post.objects.filter(status="published")     # drafts will 404
```

### Summary of defaults

| View | Template | Context variables | URL kwarg |
|---|---|---|---|
| `ListView` | `<app>/<model>_list.html` | `object_list`, `<model>_list` | none |
| `DetailView` | `<app>/<model>_detail.html` | `object`, `<model>` | `pk` or `slug` |

---

## 11.3 Create, update, and delete views

Django also provides generic views for the rest of CRUD (Create, Read, Update, Delete). They combine forms (Lesson 10) with the model.

### CreateView

```python
from django.urls import reverse_lazy
from django.views.generic import CreateView
from .models import Post


class PostCreateView(CreateView):
    model = Post
    fields = ["title", "slug", "body", "status"]
    success_url = reverse_lazy("blog:post_list")
```

What it does:

- `GET` shows an empty ModelForm.
- `POST` validates the form; if valid, saves the object and redirects; if invalid, redisplays the form with errors.
- The default template is `blog/post_form.html` (`<app>/<model>_form.html`), and the form is `form` in the context.
- If you omit `success_url`, Django redirects to the new object's `get_absolute_url()` (Lesson 5), which is why defining that method is so useful.

Use `reverse_lazy()`, **not** `reverse()`, for class attributes: URLs have not been loaded yet when the class is defined.

### Using your own form class

Use `form_class` instead of `fields` when you want custom validation or widgets:

```python
from .forms import PostForm


class PostCreateView(CreateView):
    model = Post
    form_class = PostForm
```

### UpdateView

```python
from django.views.generic import UpdateView


class PostUpdateView(UpdateView):
    model = Post
    fields = ["title", "body", "status"]
    # success_url omitted: falls back to post.get_absolute_url()
```

It finds the object by `pk` or `slug` from the URL (like `DetailView`), pre-fills the form, and uses the **same** template as `CreateView` (`post_form.html`).

### DeleteView

```python
from django.views.generic import DeleteView


class PostDeleteView(DeleteView):
    model = Post
    success_url = reverse_lazy("blog:post_list")
```

- `GET` shows a confirmation page, template `blog/post_confirm_delete.html`.
- `POST` deletes the object and redirects to `success_url` (which is **required** for `DeleteView`, since the object no longer exists to supply a URL).

Confirmation template:

```html
<form method="post">
  {% csrf_token %}
  <p>Delete "{{ object.title }}"? This cannot be undone.</p>
  <button type="submit">Yes, delete</button>
  <a href="{{ object.get_absolute_url }}">Cancel</a>
</form>
```

### The shared form template

```html
{% extends "base.html" %}

{% block content %}
  <h1>{% if object %}Edit{% else %}New{% endif %} post</h1>
  <form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Save</button>
  </form>
{% endblock %}
```

### Customizing form handling

Override `form_valid()` to run code before the object is saved, for example to set the author:

```python
class PostCreateView(CreateView):
    model = Post
    fields = ["title", "slug", "body"]

    def form_valid(self, form):
        form.instance.author = self.request.user     # set a field not on the form
        return super().form_valid(form)               # saves and redirects
```

Override `get_success_url()` when the redirect depends on the saved object:

```python
    def get_success_url(self):
        return reverse("blog:post_detail", kwargs={"pk": self.object.pk})
```

Add a success message with `SuccessMessageMixin`:

```python
from django.contrib.messages.views import SuccessMessageMixin


class PostCreateView(SuccessMessageMixin, CreateView):
    model = Post
    fields = ["title", "body"]
    success_message = "Post %(title)s was created."
```

### URLs for a complete CRUD set

```python
urlpatterns = [
    path("", views.PostListView.as_view(), name="post_list"),
    path("new/", views.PostCreateView.as_view(), name="post_create"),
    path("<int:pk>/", views.PostDetailView.as_view(), name="post_detail"),
    path("<int:pk>/edit/", views.PostUpdateView.as_view(), name="post_update"),
    path("<int:pk>/delete/", views.PostDeleteView.as_view(), name="post_delete"),
]
```

Put fixed paths like `new/` before patterns that could also match them (such as a `<slug:slug>/` pattern), since Django uses the first match.

---

## 11.4 Mixins

A **mixin** is a small class that adds one piece of behavior and is meant to be combined with other classes through multiple inheritance. Django's generic views are built from mixins, and Django provides ready-made ones for common concerns such as access control.

### The order of base classes matters

Python resolves methods from left to right (the **method resolution order**, MRO). Put mixins **before** the main view class:

```python
class MyView(LoginRequiredMixin, ListView):    # correct
    ...

class MyView(ListView, LoginRequiredMixin):    # wrong: the mixin may never run
    ...
```

### LoginRequiredMixin

Redirects anonymous users to the login page (the `LOGIN_URL` setting, Lesson 12):

```python
from django.contrib.auth.mixins import LoginRequiredMixin


class MyPostsView(LoginRequiredMixin, ListView):
    model = Post
    context_object_name = "posts"

    def get_queryset(self):
        return Post.objects.filter(author=self.request.user)
```

Optional attributes: `login_url`, `redirect_field_name`, and `raise_exception = True` (which returns 403 instead of redirecting).

### PermissionRequiredMixin

Checks that the user has a given permission (Lesson 12):

```python
from django.contrib.auth.mixins import PermissionRequiredMixin


class PostDeleteView(PermissionRequiredMixin, DeleteView):
    model = Post
    permission_required = "blog.delete_post"        # <app_label>.<action>_<model>
    success_url = reverse_lazy("blog:post_list")
```

`permission_required` can also be a list; the user must have all of them.

### UserPassesTestMixin

For custom rules, such as "only the author may edit":

```python
from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin


class PostUpdateView(LoginRequiredMixin, UserPassesTestMixin, UpdateView):
    model = Post
    fields = ["title", "body"]

    def test_func(self):
        post = self.get_object()
        return post.author == self.request.user
```

If `test_func()` returns `False`, the user gets a 403 (or is redirected to login if not authenticated).

### Writing your own mixin

A mixin is just a class without a base view:

```python
class OwnerQuerysetMixin:
    """Limit a view's queryset to objects owned by the current user."""

    def get_queryset(self):
        return super().get_queryset().filter(author=self.request.user)


class MyPostUpdateView(LoginRequiredMixin, OwnerQuerysetMixin, UpdateView):
    model = Post
    fields = ["title", "body"]
```

Use `super()` to cooperate with the next class in the chain.

A mixin for shared context:

```python
class CategoriesContextMixin:
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["categories"] = Category.objects.all()
        return context
```

### Useful built-in mixins

| Mixin | Provides |
|---|---|
| `LoginRequiredMixin` | Require an authenticated user |
| `PermissionRequiredMixin` | Require specific permissions |
| `UserPassesTestMixin` | Require a custom test to pass |
| `SuccessMessageMixin` | Flash a message after a successful form |
| `ContextMixin` | Supplies `get_context_data()` |
| `SingleObjectMixin` / `MultipleObjectMixin` | Single-object or list-of-objects behavior |
| `FormMixin` | Form handling for custom views |

### Debugging the MRO

If you are unsure what runs when, print the class's resolution order:

```python
print(PostUpdateView.__mro__)
```

The excellent reference site **Classy Class-Based Views** (`ccbv.co.uk`) lets you browse every attribute and method of every generic view.

### When to avoid class-based views

If you find yourself overriding many methods, or the inheritance chain is hard to follow, a plain function view (Lesson 3) is often simpler and clearer. Clever inheritance can make CBVs harder to read than the problem they solve.

---

[Previous](./[10]-Forms-And-Validation.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[12]-Authentication-And-Permissions.md)
