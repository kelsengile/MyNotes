[Previous](./[3]-URLs-And-Views.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[5]-Models-And-The-ORM.md)

*Basics*

# Lesson 4 - Templates

---

## 4.1 Django Template Language

Templates separate how a page **looks** from how it is **built**. A template is a text file (usually HTML) containing special syntax that Django replaces with real data. This syntax is the **Django Template Language (DTL)**.

### Where templates live

With the default settings (`APP_DIRS = True`), Django looks for a `templates/` folder inside each installed app. The recommended convention is to **namespace** templates by putting them in a subfolder with the app's name:

```
blog/
└── templates/
    └── blog/
        ├── post_list.html
        └── post_detail.html
```

You then refer to the template as `"blog/post_list.html"`. Namespacing prevents two apps that both have an `index.html` from colliding.

For project-wide templates (like `base.html` or `404.html`) create a `templates/` folder at the project root and add it to `DIRS` in the `TEMPLATES` setting:

```python
"DIRS": [BASE_DIR / "templates"],
```

### The three kinds of syntax

| Syntax | Name | Purpose |
|---|---|---|
| `{{ variable }}` | Variable | Outputs a value |
| `{% tag %}` | Tag | Logic such as loops and conditions |
| `{# comment #}` | Comment | Ignored, never sent to the browser |

### Variables

Variables come from the **context** dictionary the view passes to `render()`:

```python
return render(request, "blog/post_detail.html", {"post": post})
```

```html
<h1>{{ post.title }}</h1>
<p>By {{ post.author.name }}</p>
```

The dot (`.`) performs a lookup. For `post.title` Django tries, in order: a dictionary key, an attribute, then a list index. If the result is callable (such as a method), Django calls it **without arguments**. You cannot pass arguments to methods from a template.

If a variable does not exist, Django silently outputs an empty string instead of crashing.

### Automatic escaping

Django escapes HTML characters by default, so `<script>` in a variable becomes `&lt;script&gt;`. This protects you from cross-site scripting (XSS) attacks. Do not disable escaping unless you are certain the content is safe.

### The if tag

```html
{% if user.is_authenticated %}
  <p>Welcome back, {{ user.username }}!</p>
{% elif request.path == "/" %}
  <p>Welcome to the home page.</p>
{% else %}
  <p>Please log in.</p>
{% endif %}
```

Supported operators include `==`, `!=`, `<`, `>`, `<=`, `>=`, `in`, `not in`, `and`, `or`, and `not`.

### The for tag

```html
<ul>
  {% for post in posts %}
    <li>{{ forloop.counter }}. {{ post.title }}</li>
  {% empty %}
    <li>No posts yet.</li>
  {% endfor %}
</ul>
```

The `{% empty %}` block runs when the list is empty. Inside a loop, the special `forloop` variable offers `forloop.counter` (starts at 1), `forloop.counter0` (starts at 0), `forloop.first`, and `forloop.last`.

### The url tag and csrf_token

```html
<a href="{% url 'blog:post_detail' post.pk %}">{{ post.title }}</a>

<form method="post">
  {% csrf_token %}
  ...
</form>
```

`{% url %}` reverses a URL by name (Lesson 3). `{% csrf_token %}` is **required** in every POST form to protect against CSRF attacks (Lesson 17).

---

## 4.2 Template inheritance

Most pages on a site share the same header, navigation, and footer. **Template inheritance** lets you define that layout once in a base template and have other templates fill in only what differs.

### The base template

Create `templates/base.html` in the project root:

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>{% block title %}My Site{% endblock %}</title>
  <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
  <nav>
    <a href="{% url 'blog:post_list' %}">Home</a>
  </nav>

  <main>
    {% block content %}{% endblock %}
  </main>

  <footer>
    <p>&copy; 2026 My Site</p>
  </footer>
</body>
</html>
```

Each `{% block name %}...{% endblock %}` defines a region that child templates may override.

### Child templates

```html
{% extends "base.html" %}

{% block title %}All Posts{% endblock %}

{% block content %}
  <h1>Latest Posts</h1>
  {% for post in posts %}
    <h2>{{ post.title }}</h2>
  {% endfor %}
{% endblock %}
```

Rules to remember:

- `{% extends %}` must be the **first tag** in the template.
- Content outside a `{% block %}` in a child template is ignored.
- Use `{{ block.super }}` inside a block to include the parent's content and add to it rather than replace it.

### Including reusable snippets

`{% include %}` inserts another template, which is ideal for small reusable pieces such as a card or a pagination control:

```html
{% include "blog/_post_card.html" with post=post %}
```

A leading underscore in the file name is a common convention for partial templates.

---

## 4.3 Filters and tags

### Filters

A **filter** transforms a variable's value. You apply one with a pipe (`|`), and you can chain several:

```html
{{ post.title|upper }}
{{ post.body|truncatewords:30 }}
{{ post.created_at|date:"F j, Y" }}
{{ post.subtitle|default:"No subtitle" }}
{{ tags|join:", " }}
```

Commonly used built-in filters:

| Filter | Result |
|---|---|
| `lower`, `upper`, `title` | Changes letter case |
| `length` | Number of items or characters |
| `default:"x"` | Fallback when the value is empty or false |
| `date:"Y-m-d"` | Formats a date or datetime |
| `truncatechars:50`, `truncatewords:20` | Shortens text |
| `linebreaks` | Converts newlines into `<p>` and `<br>` tags |
| `floatformat:2` | Rounds a number to 2 decimal places |
| `pluralize` | Adds "s" when a count is not 1 |
| `safe` | Marks a string as safe HTML (turns off escaping) |
| `join:", "` | Joins a list into a string |

Example of `pluralize`:

```html
{{ count }} comment{{ count|pluralize }}
```

Use `|safe` very carefully. Only apply it to content you fully control, never to user-submitted text.

### More built-in tags

| Tag | Purpose |
|---|---|
| `{% with total=items\|length %}...{% endwith %}` | Creates a temporary variable |
| `{% now "Y" %}` | Prints the current date or time |
| `{% load static %}` | Loads a template tag library |
| `{% cycle "odd" "even" %}` | Alternates between values in a loop |
| `{% comment %}...{% endcomment %}` | Multi-line comment |
| `{% spaceless %}...{% endspaceless %}` | Removes whitespace between HTML tags |

### Custom filters and tags

When the built-ins are not enough, write your own. Inside an app, create a `templatetags/` package (with an `__init__.py`) and a module:

```python
# blog/templatetags/blog_extras.py
from django import template

register = template.Library()


@register.filter
def initials(value):
    """Return the first letter of each word."""
    return "".join(word[0] for word in value.split())


@register.simple_tag
def site_name():
    return "My Site"
```

Use them by loading the module by file name:

```html
{% load blog_extras %}
{{ post.author.name|initials }}
{% site_name %}
```

Restart the development server after adding a new `templatetags` package so Django registers it.

---

## 4.4 Static files

**Static files** are assets that do not change per request: CSS, JavaScript, images, and fonts. Django's `staticfiles` app helps you manage them.

### Where to put them

Inside each app, use a namespaced `static/` folder (same idea as templates):

```
blog/
└── static/
    └── blog/
        ├── css/style.css
        └── js/app.js
```

For project-wide assets, create a `static/` folder at the project root and register it:

```python
# settings.py
STATIC_URL = "static/"
STATICFILES_DIRS = [BASE_DIR / "static"]
```

### Using static files in templates

Load the `static` tag library, then use the `{% static %}` tag, which builds the correct URL for you:

```html
{% load static %}
<link rel="stylesheet" href="{% static 'blog/css/style.css' %}">
<script src="{% static 'blog/js/app.js' %}"></script>
<img src="{% static 'blog/img/logo.png' %}" alt="Logo">
```

Each template that uses `{% static %}` needs its own `{% load static %}` line; loading it in a parent does not carry over to child templates.

### Development vs production

When `DEBUG = True`, `runserver` serves static files automatically. In production, you collect them into one folder with `collectstatic` and serve them with a real web server or CDN. Both are covered in Lesson 14 and Lesson 19.

---

[Previous](./[3]-URLs-And-Views.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[5]-Models-And-The-ORM.md)
