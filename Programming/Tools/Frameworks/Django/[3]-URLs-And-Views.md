[Previous](./[2]-Projects-And-Apps.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[4]-Templates.md)

*Basics*

# Lesson 3 - URLs And Views

---

## 3.1 URL routing

Every page on a Django site is reached through a **URL pattern** that points to a **view**. This mapping is called the **URLconf** (URL configuration).

### The root URLconf

Django starts at the file named in `ROOT_URLCONF` (usually `mysite/urls.py`) and looks for a list called `urlpatterns`:

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("blog/", include("blog.urls")),
]
```

Django checks the patterns **from top to bottom** and uses the first one that matches.

### App-level URLs with include()

Each app should own its URLs. Create `blog/urls.py`:

```python
# blog/urls.py
from django.urls import path
from . import views

app_name = "blog"

urlpatterns = [
    path("", views.post_list, name="post_list"),
    path("<int:pk>/", views.post_detail, name="post_detail"),
]
```

With the root config above, `/blog/` runs `post_list` and `/blog/5/` runs `post_detail` with `pk=5`. The `include()` function strips the matched prefix (`blog/`) and passes the remainder to the app's URLconf.

### path() and converters

`path(route, view, name=None)` uses **path converters** to capture parts of the URL and pass them to the view as arguments:

| Converter | Matches | Example |
|---|---|---|
| `str` | Any non-empty string without `/` (the default) | `<str:username>` |
| `int` | Zero or a positive integer | `<int:pk>` |
| `slug` | Letters, numbers, hyphens, underscores | `<slug:slug>` |
| `uuid` | A formatted UUID | `<uuid:id>` |
| `path` | Any non-empty string including `/` | `<path:file_path>` |

For complex patterns, `re_path()` accepts regular expressions, but `path()` is preferred whenever possible.

### Naming URLs and reversing them

The `name` argument gives a pattern a stable identifier. Instead of hard-coding URLs, **reverse** them by name. If you change a URL's shape later, everything that reverses it keeps working.

```python
from django.urls import reverse

reverse("blog:post_detail", args=[5])          # "/blog/5/"
reverse("blog:post_detail", kwargs={"pk": 5})  # "/blog/5/"
```

The `blog:` prefix is the **namespace**, set by `app_name = "blog"`. Namespaces stop two apps that both use a name like `detail` from colliding. In templates you use the `{% url %}` tag (Lesson 4):

```
<a href="{% url 'blog:post_detail' post.pk %}">Read more</a>
```

---

## 3.2 Function-based views

A **view** is a Python callable that takes a request and returns a response. The simplest kind is a plain function. Views live in `views.py`.

### A minimal view

```python
# blog/views.py
from django.http import HttpResponse


def home(request):
    return HttpResponse("Hello, Django!")
```

Every view receives an `HttpRequest` object as its first argument and **must** return an `HttpResponse` (or a subclass) or raise an exception.

### Views that receive URL arguments

Captured URL values arrive as extra arguments:

```python
def post_detail(request, pk):
    return HttpResponse(f"You are viewing post number {pk}")
```

### Rendering a template

Most views fetch data and pass it to a template:

```python
from django.shortcuts import render
from .models import Post


def post_list(request):
    posts = Post.objects.order_by("-created_at")
    return render(request, "blog/post_list.html", {"posts": posts})
```

`render(request, template_name, context)` loads the template, fills it with the context dictionary, and returns an `HttpResponse`. (Models are explained in Lesson 5.)

### Restricting HTTP methods

Use decorators to allow only certain methods:

```python
from django.views.decorators.http import require_http_methods, require_POST


@require_http_methods(["GET", "POST"])
def contact(request):
    ...


@require_POST
def delete_comment(request, pk):
    ...
```

A disallowed method receives a `405 Method Not Allowed` response.

---

## 3.3 Request and response objects

### The HttpRequest object

Django creates an `HttpRequest` for every incoming request and passes it to your view. Common attributes:

| Attribute | Description |
|---|---|
| `request.method` | The HTTP method as an uppercase string (`"GET"`, `"POST"`, ...) |
| `request.GET` | Dictionary-like object of query-string parameters (`?q=django`) |
| `request.POST` | Dictionary-like object of submitted form data |
| `request.FILES` | Uploaded files |
| `request.COOKIES` | Dictionary of cookies sent by the browser |
| `request.session` | The user's session data (Lesson 13) |
| `request.user` | The current user, or an `AnonymousUser` (Lesson 12) |
| `request.path` | The requested path, e.g. `"/blog/5/"` |
| `request.headers` | Case-insensitive access to HTTP headers |
| `request.META` | A dictionary of all raw headers and server variables |
| `request.body` | The raw request body as bytes |

Example reading a query parameter:

```python
def search(request):
    query = request.GET.get("q", "")   # "" if missing
    return HttpResponse(f"Searching for: {query}")
```

Use `.get()` rather than square brackets so a missing key does not raise an error.

### Handling different methods

```python
def contact(request):
    if request.method == "POST":
        name = request.POST.get("name")
        # process the data...
        return HttpResponse("Thanks!")
    return render(request, "blog/contact.html")
```

### The HttpResponse object

You create and return responses yourself:

```python
from django.http import HttpResponse, JsonResponse

response = HttpResponse("Plain text", content_type="text/plain", status=200)
response["X-Custom-Header"] = "value"
response.set_cookie("theme", "dark")
```

Useful subclasses:

| Class | Purpose |
|---|---|
| `HttpResponse` | A general response with content you provide |
| `JsonResponse` | Serializes a dictionary to JSON and sets the right content type |
| `HttpResponseRedirect` | A 302 redirect to another URL |
| `HttpResponseNotFound` | A 404 response |
| `FileResponse` | Streams a file efficiently |

```python
def api_status(request):
    return JsonResponse({"status": "ok", "version": 1})
```

Common status codes: `200` OK, `201` Created, `301/302` redirects, `400` Bad Request, `403` Forbidden, `404` Not Found, `500` Server Error.

---

## 3.4 Shortcuts and error pages

`django.shortcuts` contains helper functions that remove boilerplate.

### render()

Covered above. It combines loading a template, rendering it, and wrapping it in an `HttpResponse`.

### redirect()

Returns a redirect. It accepts a URL, a URL name, or a model instance (which uses its `get_absolute_url()` method):

```python
from django.shortcuts import redirect

def old_page(request):
    return redirect("blog:post_list")

def after_save(request, pk):
    return redirect("blog:post_detail", pk=pk)
```

### get_object_or_404()

Fetches a single object or raises a 404 error if it does not exist, which is the correct behavior for a missing page:

```python
from django.shortcuts import get_object_or_404, render
from .models import Post


def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, "blog/post_detail.html", {"post": post})
```

There is also `get_list_or_404()` for queries that should return at least one result.

### Raising Http404 manually

```python
from django.http import Http404

def post_detail(request, pk):
    if pk > 1000:
        raise Http404("Post does not exist")
    ...
```

### Custom error pages

Django looks for templates named `404.html`, `500.html`, `403.html`, and `400.html` in your templates directory. Create `templates/404.html` to customize the "not found" page:

```html
<h1>Page not found</h1>
<p>Sorry, we couldn't find that page.</p>
```

Important details:

- Custom error pages only appear when `DEBUG = False`. With `DEBUG = True`, Django shows its detailed debug page instead.
- When `DEBUG = False`, you must set `ALLOWED_HOSTS` or the server will refuse requests.
- For advanced control, assign your own view in the root URLconf: `handler404 = "mysite.views.custom_404"`.

---

[Previous](./[2]-Projects-And-Apps.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[4]-Templates.md)
