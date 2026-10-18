[Previous](./[4]-Jinja2-Templates.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[6]-Application-Factory-And-Blueprints.md)

*Building Applications*

# Lesson 5 - Forms With Flask-WTF

---

## 5.1 Form Classes

Handling forms by hand with `request.form` works, but you must write validation, error messages, and security checks yourself. **Flask-WTF** wraps the **WTForms** library to give you:

- Forms defined as **Python classes**.
- Built-in **validation** with helpful error messages.
- Automatic **CSRF protection**.
- Easy HTML rendering.

### Installation

```bash
pip install Flask-WTF
```

If you use the `Email()` validator, also install:

```bash
pip install email-validator
```

### Required configuration

Flask-WTF needs a `SECRET_KEY` to sign CSRF tokens:

```python
from flask import Flask

app = Flask(__name__)
app.config["SECRET_KEY"] = "change-me-in-real-projects"
```

> In real projects, load the key from an environment variable (see Lesson 7). Never commit it to version control.

### Defining a form

```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, TextAreaField, BooleanField, SubmitField


class RegistrationForm(FlaskForm):
    username = StringField("Username")
    email = StringField("Email")
    password = PasswordField("Password")
    bio = TextAreaField("About you")
    accept_terms = BooleanField("I accept the terms")
    submit = SubmitField("Create account")
```

Each class attribute is a **field**. The first argument is its label.

### Common field types

| Field | HTML result |
|---|---|
| `StringField` | Single-line text input |
| `PasswordField` | Password input |
| `TextAreaField` | Multi-line text box |
| `IntegerField` | Number input |
| `BooleanField` | Checkbox |
| `SelectField` | Dropdown (needs `choices=[(value, label), ...]`) |
| `RadioField` | Radio buttons |
| `DateField` | Date input |
| `FileField` | File upload (from `flask_wtf.file`) |
| `SubmitField` | Submit button |

### Using the form in a view

```python
from flask import render_template, redirect, url_for


@app.route("/register", methods=["GET", "POST"])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        # create the user here
        return redirect(url_for("home"))
    return render_template("register.html", form=form)
```

- On a **GET** request, `validate_on_submit()` is `False`, so the empty form is shown.
- On a **POST** request, it runs validation and returns `True` only when everything is valid.

### Rendering the form in a template

`templates/register.html`:

```html
{% extends "base.html" %}

{% block content %}
<h1>Register</h1>

<form method="post" novalidate>
  {{ form.hidden_tag() }}

  <p>
    {{ form.username.label }}<br>
    {{ form.username(size=32) }}
  </p>

  <p>
    {{ form.email.label }}<br>
    {{ form.email() }}
  </p>

  <p>
    {{ form.password.label }}<br>
    {{ form.password() }}
  </p>

  <p>{{ form.accept_terms() }} {{ form.accept_terms.label }}</p>

  <p>{{ form.submit() }}</p>
</form>
{% endblock %}
```

- `form.field()` renders the input; you can pass HTML attributes such as `class="input"` or `placeholder="..."`.
- `form.hidden_tag()` renders hidden inputs, including the CSRF token (see 5.3).
- `novalidate` turns off the browser's built-in validation so WTForms messages are shown consistently.

### Accessing submitted data

After validation, each field's cleaned value is in `.data`:

```python
username = form.username.data
email = form.email.data
```

---

## 5.2 Validation

Validators are attached to a field and run when the form is submitted.

```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, Length, EqualTo


class RegistrationForm(FlaskForm):
    username = StringField(
        "Username",
        validators=[DataRequired(), Length(min=3, max=20)],
    )
    email = StringField(
        "Email",
        validators=[DataRequired(), Email()],
    )
    password = PasswordField(
        "Password",
        validators=[DataRequired(), Length(min=8)],
    )
    confirm_password = PasswordField(
        "Confirm password",
        validators=[DataRequired(), EqualTo("password", message="Passwords must match.")],
    )
    submit = SubmitField("Create account")
```

### Common validators

| Validator | Checks that... |
|---|---|
| `DataRequired()` | The field is not empty. |
| `InputRequired()` | Something was submitted (useful for numbers where `0` is valid). |
| `Optional()` | The field may be left blank; skips other validators if empty. |
| `Length(min, max)` | Text length is within a range. |
| `Email()` | The value looks like an email address. |
| `EqualTo("other")` | It matches another field. |
| `NumberRange(min, max)` | A number is within a range. |
| `Regexp(pattern)` | The text matches a regular expression. |
| `URL()` | The value is a valid URL. |
| `AnyOf([...])` / `NoneOf([...])` | The value is (or is not) in a list. |

### Custom validators

Define a method named `validate_<fieldname>` on the form class and raise `ValidationError` when something is wrong:

```python
from wtforms.validators import ValidationError

RESERVED_NAMES = {"admin", "root", "support"}


class RegistrationForm(FlaskForm):
    username = StringField("Username", validators=[DataRequired()])
    submit = SubmitField("Create account")

    def validate_username(self, field):
        if field.data.lower() in RESERVED_NAMES:
            raise ValidationError("That username is reserved.")
```

For checks that need the database (such as uniqueness), do the query inside this method. Lesson 10 shows how to query.

### Showing errors in the template

When validation fails, each field's `errors` list holds its messages:

```html
<p>
  {{ form.username.label }}<br>
  {{ form.username() }}
  {% for error in form.username.errors %}
    <span class="error">{{ error }}</span>
  {% endfor %}
</p>
```

To avoid repeating this for every field, create a macro:

```html
{% macro render_field(field) %}
  <p>
    {{ field.label }}<br>
    {{ field(**kwargs) }}
    {% for error in field.errors %}
      <span class="error">{{ error }}</span>
    {% endfor %}
  </p>
{% endmacro %}
```

Then use it as `{{ render_field(form.email, class="input") }}`.

### Validation happens on the server

Browser-side checks (like the HTML `required` attribute) are conveniences only. A user can bypass them, so **server-side validation is the one that counts**.

---

## 5.3 CSRF Protection

**Cross-Site Request Forgery (CSRF)** is an attack where a malicious website causes a logged-in user's browser to send a request to your site without the user knowing (for example, a hidden form that submits a "change email" request).

### How the defense works

1. When your form page is rendered, Flask-WTF generates a **secret token** tied to the user's session.
2. The token is embedded in the form as a hidden input.
3. When the form is submitted, the server checks the token. A forged request from another site cannot know the token, so it is rejected.

### What you must do

- Set a `SECRET_KEY`.
- Include `{{ form.hidden_tag() }}` (or `{{ form.csrf_token }}`) inside every POST form.

```html
<form method="post">
  {{ form.hidden_tag() }}
  ...
</form>
```

If the token is missing or invalid, validation fails and `form.errors` includes a `csrf_token` entry.

### Protecting routes that do not use a FlaskForm

Enable global CSRF protection with `CSRFProtect`. Then every POST, PUT, PATCH, and DELETE request must include a valid token:

```python
from flask_wtf.csrf import CSRFProtect

csrf = CSRFProtect(app)
```

In a plain HTML form without a `FlaskForm`:

```html
<form method="post" action="/delete-account">
  <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
  <button type="submit">Delete</button>
</form>
```

For JavaScript `fetch` requests, send the token in a header:

```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

```javascript
const token = document.querySelector('meta[name="csrf-token"]').content;

fetch("/api/settings", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-CSRFToken": token,
  },
  body: JSON.stringify({ theme: "dark" }),
});
```

### Exempting a view

A JSON API authenticated with tokens (not cookies) may not need CSRF protection:

```python
@csrf.exempt
@app.post("/api/webhook")
def webhook():
    return {"received": True}
```

Only exempt endpoints when you understand why they are safe.

### Handling a CSRF failure

```python
from flask_wtf.csrf import CSRFError


@app.errorhandler(CSRFError)
def handle_csrf_error(error):
    return render_template("csrf_error.html", reason=error.description), 400
```

### Tests

During automated tests it is common to disable CSRF checks: `app.config["WTF_CSRF_ENABLED"] = False`. Never do this in production.

---

## 5.4 Flash Messages

**Flash messages** are one-time notices shown to the user on the *next* page they see, such as "Account created!" after a redirect.

### Sending a message

```python
from flask import flash, redirect, url_for


@app.route("/register", methods=["GET", "POST"])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        flash("Account created successfully!", "success")
        return redirect(url_for("login"))
    return render_template("register.html", form=form)
```

- The first argument is the message.
- The second argument is an optional **category** (`"success"`, `"error"`, `"info"`, `"warning"`) that you can use for styling.

Flash messages are stored in the session, so a `SECRET_KEY` is required.

### Displaying messages in the base template

Put this in `base.html` so every page can show messages:

```html
{% with messages = get_flashed_messages(with_categories=true) %}
  {% if messages %}
    <ul class="flashes">
      {% for category, message in messages %}
        <li class="flash flash-{{ category }}">{{ message }}</li>
      {% endfor %}
    </ul>
  {% endif %}
{% endwith %}
```

Once displayed, the messages are removed from the session automatically.

### Styling by category

```css
.flash { padding: 0.75rem 1rem; border-radius: 4px; margin-bottom: 0.5rem; }
.flash-success { background: #d4edda; color: #155724; }
.flash-error   { background: #f8d7da; color: #721c24; }
.flash-info    { background: #d1ecf1; color: #0c5460; }
```

### A complete form flow

```python
@app.route("/contact", methods=["GET", "POST"])
def contact():
    form = ContactForm()
    if form.validate_on_submit():
        # send the email, save to the database, etc.
        flash("Thanks! We'll be in touch.", "success")
        return redirect(url_for("contact"))   # Post/Redirect/Get
    if form.errors:
        flash("Please fix the errors below.", "error")
    return render_template("contact.html", form=form)
```

Redirecting after a successful POST means refreshing the page does not resubmit the form.

---

[Previous](./[4]-Jinja2-Templates.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[6]-Application-Factory-And-Blueprints.md)
