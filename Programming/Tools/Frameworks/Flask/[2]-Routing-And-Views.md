[Previous](./[1]-What-Is-Flask.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[3]-Requests-And-Responses.md)

*Basics*

# Lesson 2 - Routing And Views

---

## 2.1 The Route Decorator

**Routing** is the process of matching an incoming URL to the Python function that should handle it. That function is called a **view** (or view function).

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def index():
    return "Home page"


@app.route("/contact")
def contact():
    return "Contact page"
```

### How it works

- `@app.route("/path")` registers the function under that URL path.
- When a request arrives, Flask compares the path against all registered rules and calls the matching view.
- The view's **return value** becomes the response.
- If nothing matches, Flask automatically returns a **404 Not Found** response.

### Multiple routes for one view

You can stack decorators so several URLs reach the same function:

```python
@app.route("/")
@app.route("/home")
def index():
    return "Home page"
```

### Trailing slashes

Flask treats these two rule styles differently:

```python
@app.route("/projects/")   # with trailing slash
def projects():
    return "Projects list"


@app.route("/about")       # without trailing slash
def about():
    return "About page"
```

| Rule | Visiting `/projects` | Visiting `/projects/` |
|---|---|---|
| `"/projects/"` | Redirects to `/projects/` | Works |
| `"/about"` | Works | **404 Not Found** |

This mirrors how folders (with a slash) and files (without) behave on a file system. Pick one convention and use it consistently.

### The endpoint name

Every route has an **endpoint**, which by default is the **function name** (`index`, `contact`, ...). Endpoints are what `url_for` uses (see 2.4). Two views in the same app cannot share the same endpoint name.

---

## 2.2 Variable Rules

Parts of a URL can be dynamic. Put the variable name inside angle brackets, and Flask passes its value to the view as an argument.

```python
@app.route("/user/<username>")
def profile(username):
    return f"Profile of {username}"
```

Visiting `/user/ada` calls `profile("ada")`.

### Converters

A **converter** restricts what the variable matches and converts it to the right Python type:

```python
@app.route("/post/<int:post_id>")
def show_post(post_id):
    return f"Post number {post_id} (type: {type(post_id).__name__})"
```

| Converter | Matches | Python type |
|---|---|---|
| `string` (default) | Any text without a slash | `str` |
| `int` | Positive integers | `int` |
| `float` | Positive floating-point numbers | `float` |
| `path` | Like `string`, but also accepts slashes | `str` |
| `uuid` | UUID strings | `uuid.UUID` |

Examples:

```python
@app.route("/price/<float:amount>")
def price(amount):
    return f"Amount: {amount:.2f}"


@app.route("/files/<path:filepath>")
def serve_path(filepath):
    return f"You asked for: {filepath}"
```

- `/post/42` works, but `/post/abc` returns **404** because `abc` is not an `int`.
- `/files/docs/readme.txt` works because `path` accepts slashes.

### Multiple variables

```python
@app.route("/blog/<int:year>/<int:month>/<slug>")
def blog_entry(year, month, slug):
    return f"{year}-{month:02d}: {slug}"
```

### Default values

You can give a view function default argument values and register two rules, so one URL omits the variable:

```python
@app.route("/page/", defaults={"number": 1})
@app.route("/page/<int:number>")
def page(number):
    return f"Page {number}"
```

---

## 2.3 HTTP Methods

HTTP defines **methods** that describe the action a request wants to perform.

| Method | Typical meaning |
|---|---|
| `GET` | Retrieve data (should not change anything). |
| `POST` | Submit data to create something or trigger an action. |
| `PUT` | Replace an existing resource. |
| `PATCH` | Partially update a resource. |
| `DELETE` | Remove a resource. |

By default a route only answers **`GET`** (plus `HEAD` and `OPTIONS`, which Flask adds automatically). To accept others, list them with `methods`:

```python
from flask import request


@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        return "Processing the submitted form"
    return "Showing the login form"
```

- A request using a method that is not allowed receives **405 Method Not Allowed**.
- `request.method` tells you which method was used.

### Method shortcuts

Flask provides decorators for common methods, which keep code tidy:

```python
@app.get("/items")
def list_items():
    return "All items"


@app.post("/items")
def create_item():
    return "Item created", 201
```

Equivalent shortcuts exist for `put`, `patch`, and `delete`.

### Splitting methods into separate views

You can give different methods their own functions on the same URL:

```python
@app.get("/notes")
def notes_list():
    return "Listing notes"


@app.post("/notes")
def notes_create():
    return "Creating a note", 201
```

> **Note:** Each of these functions needs a distinct name because the function name is the endpoint.

---

## 2.4 URL Building With url_for

Hard-coding URLs like `"/user/ada"` throughout your code is fragile: if a route changes, every copy must be found and fixed. Instead, ask Flask to **build** the URL from the endpoint name using `url_for`.

```python
from flask import Flask, url_for

app = Flask(__name__)


@app.route("/")
def index():
    return "Home"


@app.route("/user/<username>")
def profile(username):
    return f"Profile of {username}"


@app.route("/post/<int:post_id>")
def show_post(post_id):
    return f"Post {post_id}"
```

To see how `url_for` behaves outside a real request, use a test request context:

```python
with app.test_request_context():
    print(url_for("index"))                         # /
    print(url_for("profile", username="ada"))       # /user/ada
    print(url_for("show_post", post_id=7))          # /post/7
    print(url_for("show_post", post_id=7, page=2))  # /post/7?page=2
    print(url_for("static", filename="style.css"))  # /static/style.css
```

### Key points

- The first argument is the **endpoint name** (the view function name).
- Arguments matching variables in the rule fill the URL path.
- **Extra** keyword arguments become **query string** parameters (`?page=2`).
- `url_for("static", filename="...")` builds URLs to static files.
- `url_for(..., _external=True)` produces a full absolute URL including the domain, useful for emails.

### Benefits

1. **Single source of truth** — change a route once, and all generated links update.
2. **Automatic escaping** — special characters are handled correctly.
3. **Works with blueprints** — endpoints are namespaced (e.g. `url_for("auth.login")`, see Lesson 6).
4. **Handles sub-path deployments** — if the app is mounted under a prefix, `url_for` still produces correct links.

### Using url_for in templates

Inside Jinja2 templates, `url_for` is available directly:

```html
<a href="{{ url_for('profile', username='ada') }}">Ada's profile</a>
```

### Redirecting with url_for

```python
from flask import redirect


@app.route("/go-home")
def go_home():
    return redirect(url_for("index"))
```

---

[Previous](./[1]-What-Is-Flask.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[3]-Requests-And-Responses.md)
