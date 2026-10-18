[Previous](./[2]-Routing-And-Views.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[4]-Jinja2-Templates.md)

*Basics*

# Lesson 3 - Requests And Responses

---

## 3.1 The Request Object

When a browser or API client contacts your app, Flask packages everything about that request into a **request object**. You import it and use it inside any view:

```python
from flask import Flask, request

app = Flask(__name__)
```

`request` looks like a global variable, but Flask makes it **context-local**: inside a view it always refers to the request currently being handled, even with many requests running at once.

### Commonly used attributes

| Attribute | Contains |
|---|---|
| `request.method` | `"GET"`, `"POST"`, etc. |
| `request.path` | The URL path, e.g. `/search` |
| `request.url` | The full URL |
| `request.args` | Query string values (`?q=flask`) |
| `request.form` | Submitted HTML form fields |
| `request.json` / `request.get_json()` | A parsed JSON body |
| `request.files` | Uploaded files |
| `request.headers` | HTTP headers |
| `request.cookies` | Cookies sent by the browser |
| `request.remote_addr` | The client's IP address (see the proxy note in Lesson 17) |

### Query string: request.args

For a URL like `/search?q=flask&page=2`:

```python
@app.route("/search")
def search():
    query = request.args.get("q", "")
    page = request.args.get("page", 1, type=int)
    return f"Searching for '{query}' on page {page}"
```

- Use `.get(key, default)` rather than `request.args[key]`. Indexing a missing key raises a **400 Bad Request** error.
- `type=int` converts the value, falling back to the default if conversion fails.
- `request.args.getlist("tag")` returns all values for a repeated key like `?tag=a&tag=b`.

### Form data: request.form

When an HTML form is submitted with `method="post"`:

```html
<form method="post" action="/greet">
  <input type="text" name="name">
  <button type="submit">Send</button>
</form>
```

```python
@app.post("/greet")
def greet():
    name = request.form.get("name", "stranger")
    return f"Hello, {name}!"
```

### JSON bodies

For API clients sending `Content-Type: application/json`:

```python
@app.post("/api/echo")
def echo():
    data = request.get_json()
    return {"you_sent": data}
```

- `request.get_json()` returns the parsed data. If the body is not valid JSON or the content type is wrong, Flask responds with an error (400 or 415).
- `request.get_json(silent=True)` returns `None` instead of raising, so you can handle the problem yourself.

### File uploads

```html
<form method="post" action="/upload" enctype="multipart/form-data">
  <input type="file" name="photo">
  <button type="submit">Upload</button>
</form>
```

```python
from werkzeug.utils import secure_filename


@app.post("/upload")
def upload():
    file = request.files.get("photo")
    if file is None or file.filename == "":
        return "No file selected", 400
    filename = secure_filename(file.filename)
    file.save(f"uploads/{filename}")
    return f"Saved {filename}"
```

> **Important:** Always pass user-supplied filenames through `secure_filename` before saving. More upload safety advice appears in Lesson 14.

### Headers

```python
@app.route("/agent")
def agent():
    user_agent = request.headers.get("User-Agent", "unknown")
    return f"Your browser says: {user_agent}"
```

---

## 3.2 Returning Responses

A view must return something Flask can turn into an HTTP response. Flask converts several return types automatically.

| Return value | What Flask does |
|---|---|
| `str` | Becomes an HTML response with status 200. |
| `dict` or `list` | Converted to a JSON response automatically. |
| `tuple` | `(body, status)`, `(body, headers)`, or `(body, status, headers)`. |
| `Response` object | Used as-is. |
| Result of `render_template` | An HTML string, handled like `str`. |

### Strings and JSON

```python
@app.route("/hello")
def hello():
    return "<p>Hello</p>"


@app.route("/api/status")
def status():
    return {"status": "ok", "version": 1}
```

The second view sends `Content-Type: application/json`.

### Tuples: setting status and headers

```python
@app.post("/things")
def create_thing():
    return {"id": 10}, 201


@app.route("/teapot")
def teapot():
    return "I'm a teapot", 418, {"X-Custom-Header": "tea"}
```

### jsonify

`jsonify()` explicitly builds a JSON response and is useful when returning something like a list built dynamically or when you want a Response object to modify:

```python
from flask import jsonify


@app.route("/api/numbers")
def numbers():
    return jsonify([1, 2, 3])
```

### Building a response object

Use `make_response` when you need to change headers or cookies before returning:

```python
from flask import make_response


@app.route("/download")
def download():
    response = make_response("name,age\nAda,36\n")
    response.headers["Content-Type"] = "text/csv"
    response.headers["Content-Disposition"] = "attachment; filename=people.csv"
    return response
```

### Returning files

```python
from flask import send_from_directory


@app.route("/reports/<path:filename>")
def reports(filename):
    return send_from_directory("reports", filename)
```

`send_from_directory` safely prevents requests from escaping the target directory.

---

## 3.3 Redirects And Status Codes

### Redirects

`redirect()` tells the browser to request a different URL:

```python
from flask import redirect, url_for


@app.route("/old-page")
def old_page():
    return redirect(url_for("new_page"))


@app.route("/new-page")
def new_page():
    return "You are on the new page"
```

By default a redirect uses status **302** (temporary). Pass `code=301` for a permanent redirect:

```python
return redirect(url_for("new_page"), code=301)
```

### Post/Redirect/Get pattern

After processing a form submission, **redirect** instead of rendering a page directly. This stops the browser from re-submitting the form when the user refreshes:

```python
@app.route("/subscribe", methods=["GET", "POST"])
def subscribe():
    if request.method == "POST":
        # save the subscription here
        return redirect(url_for("thanks"))
    return "Subscription form"


@app.route("/thanks")
def thanks():
    return "Thank you for subscribing!"
```

### Aborting with an error

`abort()` immediately stops the view and returns an HTTP error:

```python
from flask import abort

USERS = {"ada": "Ada Lovelace", "alan": "Alan Turing"}


@app.route("/people/<username>")
def person(username):
    if username not in USERS:
        abort(404)
    return USERS[username]
```

### Common status codes

| Code | Name | When to use |
|---|---|---|
| 200 | OK | Successful request (default). |
| 201 | Created | A new resource was created. |
| 204 | No Content | Success with nothing to return (e.g. deletion). |
| 301 / 302 | Moved / Found | Redirects (permanent / temporary). |
| 400 | Bad Request | The client sent invalid data. |
| 401 | Unauthorized | Authentication is needed. |
| 403 | Forbidden | Authenticated but not allowed. |
| 404 | Not Found | The resource does not exist. |
| 405 | Method Not Allowed | Wrong HTTP method for this route. |
| 500 | Internal Server Error | Something broke on the server. |

Status codes group by first digit: **2xx** success, **3xx** redirect, **4xx** client error, **5xx** server error.

---

## 3.4 Cookies And Headers

### Reading cookies

Cookies the browser sends arrive in `request.cookies`:

```python
@app.route("/welcome")
def welcome():
    name = request.cookies.get("username", "guest")
    return f"Welcome back, {name}"
```

### Setting cookies

To set a cookie, attach it to a response object:

```python
@app.route("/set-name/<name>")
def set_name(name):
    response = make_response(f"Cookie set for {name}")
    response.set_cookie("username", name, max_age=60 * 60 * 24 * 7)
    return response
```

### Deleting cookies

```python
@app.route("/forget")
def forget():
    response = make_response("Cookie removed")
    response.delete_cookie("username")
    return response
```

### Important cookie options

| Option | Purpose |
|---|---|
| `max_age` | Lifetime in seconds. Without it, the cookie lasts only until the browser closes. |
| `httponly=True` | Prevents JavaScript from reading the cookie, reducing XSS risk. |
| `secure=True` | Sends the cookie only over HTTPS. |
| `samesite="Lax"` | Limits sending the cookie on cross-site requests, helping against CSRF. |

```python
response.set_cookie(
    "theme",
    "dark",
    max_age=3600,
    httponly=True,
    secure=True,
    samesite="Lax",
)
```

> **Note:** Plain cookies can be read and edited by the user. Never store sensitive data in them. For logged-in state, use Flask's signed `session` (Lesson 8).

### Custom response headers

```python
@app.route("/with-headers")
def with_headers():
    response = make_response("Check the response headers")
    response.headers["X-App-Version"] = "1.0"
    response.headers["Cache-Control"] = "no-store"
    return response
```

### Adding headers to every response

The `after_request` hook runs after each view and lets you modify the response globally:

```python
@app.after_request
def add_headers(response):
    response.headers["X-Content-Type-Options"] = "nosniff"
    return response
```

The hook function must **always return the response**.

---

[Previous](./[2]-Routing-And-Views.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[4]-Jinja2-Templates.md)
