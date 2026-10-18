[Previous](./[3]-Requests-And-Responses.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[5]-Forms-With-Flask-WTF.md)

*Basics*

# Lesson 4 - Jinja2 Templates

---

## 4.1 Rendering Templates

Building HTML by gluing strings together inside Python gets messy fast. A **template** is an HTML file with placeholders that Flask fills with data. Flask uses the **Jinja2** template engine.

### Folder layout

Flask looks for templates in a folder named `templates` next to your application:

```
flask-demo/
├── app.py
└── templates/
    └── hello.html
```

### A template file

`templates/hello.html`:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Hello</title>
  </head>
  <body>
    <h1>Hello, {{ name }}!</h1>
  </body>
</html>
```

### Rendering it from a view

```python
from flask import Flask, render_template

app = Flask(__name__)


@app.route("/hello/<name>")
def hello(name):
    return render_template("hello.html", name=name)
```

- The first argument is the template filename (relative to `templates/`).
- Every extra keyword argument becomes a variable available inside the template.

### Three kinds of Jinja2 syntax

| Syntax | Purpose | Example |
|---|---|---|
| `{{ ... }}` | **Output** an expression | `{{ user.name }}` |
| `{% ... %}` | **Statements** (loops, conditions, blocks) | `{% if admin %}` |
| `{# ... #}` | **Comments** (not sent to the browser) | `{# note to self #}` |

### Automatic escaping

By default, Flask **escapes** values in `.html` templates. If a variable contains `<script>alert(1)</script>`, the browser receives harmless escaped text rather than a working script. This protects against cross-site scripting (XSS).

> **Warning:** The `|safe` filter turns escaping **off** for a value. Use it only on content you fully trust and have sanitized.

### Other helpful objects available in templates

Flask automatically makes these available in every template: `request`, `session`, `g`, `config`, `url_for()`, and `get_flashed_messages()`.

---

## 4.2 Variables, Loops, And Conditions

### Variables

You can pass any Python object. Jinja2 lets you reach inside with a dot or brackets:

```python
@app.route("/profile")
def profile():
    user = {"name": "Ada", "email": "ada@example.com", "skills": ["math", "code"]}
    return render_template("profile.html", user=user)
```

```html
<p>Name: {{ user.name }}</p>
<p>Email: {{ user["email"] }}</p>
```

If an attribute or key is missing, Jinja2 gives an empty value instead of crashing.

### Filters

A **filter** transforms a value. Apply one with the pipe `|`:

```html
<p>{{ user.name | upper }}</p>
<p>{{ user.name | length }} characters</p>
<p>{{ bio | default("No bio yet") }}</p>
<p>{{ description | truncate(60) }}</p>
<p>{{ skills | join(", ") }}</p>
<p>{{ price | round(2) }}</p>
```

Useful built-in filters:

| Filter | Result |
|---|---|
| `upper`, `lower`, `title` | Change case |
| `length` | Number of items or characters |
| `default(x)` | Fallback when a value is undefined |
| `join(sep)` | Join a list into a string |
| `truncate(n)` | Shorten long text |
| `round(n)` | Round a number |
| `safe` | Mark text as trusted HTML (use carefully) |
| `tojson` | Output a value as JSON for use in JavaScript |

### Conditions

```html
{% if user.is_admin %}
  <p>Welcome, administrator.</p>
{% elif user.is_member %}
  <p>Welcome, member.</p>
{% else %}
  <p>Please sign up.</p>
{% endif %}
```

Every block statement needs a matching `end` tag.

### Loops

```python
@app.route("/skills")
def skills():
    items = ["Python", "Flask", "SQL"]
    return render_template("skills.html", items=items)
```

```html
<ul>
  {% for item in items %}
    <li>{{ loop.index }}. {{ item }}</li>
  {% else %}
    <li>No items found.</li>
  {% endfor %}
</ul>
```

- The `{% else %}` branch runs when the sequence is empty.
- Inside loops, the special `loop` object provides:

| Variable | Meaning |
|---|---|
| `loop.index` | Current iteration, starting at 1 |
| `loop.index0` | Current iteration, starting at 0 |
| `loop.first` / `loop.last` | `True` on the first or last item |
| `loop.length` | Total number of items |

### Looping over dictionaries

```html
<dl>
  {% for key, value in user.items() %}
    <dt>{{ key }}</dt>
    <dd>{{ value }}</dd>
  {% endfor %}
</dl>
```

### Macros: reusable template functions

```html
{% macro input_field(name, label, type="text") %}
  <label for="{{ name }}">{{ label }}</label>
  <input id="{{ name }}" name="{{ name }}" type="{{ type }}">
{% endmacro %}

{{ input_field("email", "Email address", "email") }}
```

Macros defined in one file can be imported into others with `{% from "macros.html" import input_field %}`.

---

## 4.3 Template Inheritance

Most pages share a common layout: header, navigation, footer. **Template inheritance** lets you write that layout once in a **base template** and have other templates **extend** it.

### The base template

`templates/base.html`:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>{% block title %}My Site{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
  </head>
  <body>
    <nav>
      <a href="{{ url_for('home') }}">Home</a>
      <a href="{{ url_for('about') }}">About</a>
    </nav>

    <main>
      {% block content %}{% endblock %}
    </main>

    <footer>
      <p>&copy; 2025 My Site</p>
    </footer>
  </body>
</html>
```

Each `{% block name %}...{% endblock %}` defines a **hole** that child templates may fill.

### A child template

`templates/home.html`:

```html
{% extends "base.html" %}

{% block title %}Home - My Site{% endblock %}

{% block content %}
  <h1>Welcome!</h1>
  <p>This content fills the "content" block of the base template.</p>
{% endblock %}
```

Rules:

- `{% extends %}` must be the **first** tag in the child template.
- Anything outside a block in a child template is ignored.
- Blocks you do not override keep the base template's default content.

### The views

```python
@app.route("/")
def home():
    return render_template("home.html")


@app.route("/about")
def about():
    return render_template("about.html")
```

### Extending a block instead of replacing it

Use `super()` to keep the parent's content and add more:

```html
{% block title %}{{ super() }} | Contact{% endblock %}
```

### Including smaller pieces

`{% include %}` inserts another template's output at that point, great for small reusable pieces:

```html
{% include "partials/_flash.html" %}
```

### Inheritance vs include

| Feature | Use it for |
|---|---|
| `extends` / `block` | The overall page layout shared by many pages |
| `include` | Small repeated snippets (a card, a message list) |
| `macro` | Reusable snippets that take parameters |

---

## 4.4 Static Files

**Static files** are assets that do not change per request: CSS, JavaScript, images, and fonts.

### Folder layout

Flask automatically serves anything in a folder named `static` at the URL path `/static/...`:

```
flask-demo/
├── app.py
├── static/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── app.js
│   └── img/
│       └── logo.png
└── templates/
    └── base.html
```

### Linking static files in templates

Always use `url_for('static', ...)` rather than typing the path:

```html
<link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
<script src="{{ url_for('static', filename='js/app.js') }}" defer></script>
<img src="{{ url_for('static', filename='img/logo.png') }}" alt="Site logo">
```

### Example stylesheet

`static/css/style.css`:

```css
body {
  font-family: system-ui, sans-serif;
  max-width: 48rem;
  margin: 2rem auto;
  padding: 0 1rem;
}

nav a {
  margin-right: 1rem;
}
```

### Customizing the static folder

```python
app = Flask(__name__, static_folder="assets", static_url_path="/assets")
```

This serves files from an `assets/` folder at `/assets/...`.

### Caching and cache busting

Browsers cache static files. When you update a file, users may still see the old version. A simple trick is adding a version query string:

```html
<link rel="stylesheet" href="{{ url_for('static', filename='css/style.css', v=2) }}">
```

This produces `/static/css/style.css?v=2`. Change the number to force a refresh.

### Production note

Flask can serve static files in development, but in production it is faster to let **Nginx** (or a CDN) serve the `static` folder directly. See Lesson 17.

---

[Previous](./[3]-Requests-And-Responses.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[5]-Forms-With-Flask-WTF.md)
