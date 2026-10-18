[Previous](./[12]-Building-JSON-APIs.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[14]-Security-Basics.md)

*Security*

# Lesson 13 - Authentication With Flask-Login

---

## 13.1 User Sessions

**Authentication** answers "Who are you?" (logging in). **Authorization** answers "What are you allowed to do?" (permissions). This lesson focuses on authentication.

### What Flask-Login does

Flask-Login manages the **user session** for you:

- Remembers which user is logged in between requests (using Flask's session cookie).
- Provides `current_user`, available in views and templates.
- Gives you `login_required` to protect routes.
- Supports optional "remember me" cookies.

It does **not** provide password storage, registration forms, or a database. You supply those.

### Installation

```bash
pip install Flask-Login
```

### The user model

Your model must provide a few properties Flask-Login expects. The easiest way is inheriting `UserMixin`:

```python
from flask_login import UserMixin
from sqlalchemy import String
from sqlalchemy.orm import Mapped, mapped_column


class User(UserMixin, db.Model):
    id: Mapped[int] = mapped_column(primary_key=True)
    username: Mapped[str] = mapped_column(String(80), unique=True)
    email: Mapped[str] = mapped_column(String(120), unique=True)
    password_hash: Mapped[str] = mapped_column(String(256))
```

`UserMixin` adds `is_authenticated`, `is_active`, `is_anonymous`, and `get_id()` with sensible defaults.

### Setting up the LoginManager

```python
from flask import Flask
from flask_login import LoginManager

login_manager = LoginManager()
login_manager.login_view = "auth.login"            # where to redirect anonymous users
login_manager.login_message = "Please log in to view that page."
login_manager.login_message_category = "info"

app = Flask(__name__)
app.config["SECRET_KEY"] = "dev-only-key"
login_manager.init_app(app)
```

### The user loader

On each request, Flask-Login reads the user ID from the session and calls your **user loader** to get the full user object:

```python
@login_manager.user_loader
def load_user(user_id):
    return db.session.get(User, int(user_id))
```

If it returns `None`, the visitor is treated as not logged in.

### How a session works here

1. After a successful login, Flask-Login stores `user_id` in Flask's signed session cookie.
2. On each request it calls `load_user(user_id)`.
3. The result becomes `current_user` for that request.

### `current_user`

```python
from flask_login import current_user


@app.route("/whoami")
def whoami():
    if current_user.is_authenticated:
        return f"Logged in as {current_user.username}"
    return "You are not logged in"
```

In templates:

```html
{% if current_user.is_authenticated %}
  <p>Hello, {{ current_user.username }}!</p>
  <a href="{{ url_for('auth.logout') }}">Log out</a>
{% else %}
  <a href="{{ url_for('auth.login') }}">Log in</a>
{% endif %}
```

---

## 13.2 Login And Logout

### Password hashing helpers (preview)

Passwords must be hashed before they are stored (see 13.3). Add these helpers to your model:

```python
from werkzeug.security import generate_password_hash, check_password_hash


class User(UserMixin, db.Model):
    # ... columns as above ...

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)
```

### Forms

```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Email, Length, EqualTo


class LoginForm(FlaskForm):
    username = StringField("Username", validators=[DataRequired()])
    password = PasswordField("Password", validators=[DataRequired()])
    remember = BooleanField("Remember me")
    submit = SubmitField("Log in")


class RegisterForm(FlaskForm):
    username = StringField("Username", validators=[DataRequired(), Length(3, 80)])
    email = StringField("Email", validators=[DataRequired(), Email()])
    password = PasswordField("Password", validators=[DataRequired(), Length(min=8)])
    confirm = PasswordField("Confirm password", validators=[DataRequired(), EqualTo("password")])
    submit = SubmitField("Register")
```

### Registration

```python
from flask import Blueprint, render_template, redirect, url_for, flash

auth_bp = Blueprint("auth", __name__)


@auth_bp.route("/register", methods=["GET", "POST"])
def register():
    form = RegisterForm()
    if form.validate_on_submit():
        existing = db.session.scalar(
            db.select(User).where(
                (User.username == form.username.data) | (User.email == form.email.data)
            )
        )
        if existing:
            flash("That username or email is already in use.", "error")
            return render_template("auth/register.html", form=form)

        user = User(username=form.username.data, email=form.email.data)
        user.set_password(form.password.data)
        db.session.add(user)
        db.session.commit()
        flash("Account created. You can now log in.", "success")
        return redirect(url_for("auth.login"))
    return render_template("auth/register.html", form=form)
```

### Logging in

```python
from urllib.parse import urlsplit
from flask import request
from flask_login import login_user, logout_user, login_required, current_user


@auth_bp.route("/login", methods=["GET", "POST"])
def login():
    if current_user.is_authenticated:
        return redirect(url_for("main.home"))

    form = LoginForm()
    if form.validate_on_submit():
        user = db.session.scalar(
            db.select(User).where(User.username == form.username.data)
        )
        if user is None or not user.check_password(form.password.data):
            flash("Invalid username or password.", "error")
            return render_template("auth/login.html", form=form)

        login_user(user, remember=form.remember.data)

        next_page = request.args.get("next")
        # Only follow relative paths to avoid open redirect attacks
        if not next_page or urlsplit(next_page).netloc != "" or not next_page.startswith("/"):
            next_page = url_for("main.home")
        return redirect(next_page)

    return render_template("auth/login.html", form=form)
```

Important details:

- `login_user(user)` writes the user ID into the session.
- `remember=True` also sets a long-lived "remember me" cookie.
- The error message is **the same** whether the username or the password was wrong, so attackers cannot discover which usernames exist.
- The `next` parameter is validated. Redirecting blindly to a user-supplied URL is an **open redirect** vulnerability.

### Logging out

```python
@auth_bp.route("/logout", methods=["POST"])
@login_required
def logout():
    logout_user()
    flash("You have been logged out.", "info")
    return redirect(url_for("auth.login"))
```

`logout_user()` clears the login from the session and removes the remember cookie. Using `POST` for logout (with a small form and CSRF token) prevents other sites from logging users out with a simple image tag or link.

```html
<form method="post" action="{{ url_for('auth.logout') }}">
  <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
  <button type="submit">Log out</button>
</form>
```

(`csrf_token()` is available in templates when `CSRFProtect` is enabled, as shown in Lesson 5.)

---

## 13.3 Password Hashing

### Never store plain-text passwords

If your database leaks and passwords are stored as plain text, every account is instantly compromised, and because people reuse passwords, their accounts on other sites are at risk too.

### What hashing is

A **hash function** turns a password into a fixed-length string that cannot feasibly be reversed. To check a login, you hash the entered password and compare it with the stored hash.

Good password hashing for storage is:

| Property | Why it matters |
|---|---|
| **Salted** | A random value is added per password, so identical passwords have different hashes and precomputed "rainbow tables" are useless. |
| **Slow on purpose** | Makes guessing millions of passwords per second expensive for attackers. |
| **One-way** | The original password cannot be computed from the hash. |

Plain fast hashes such as MD5, SHA-1, or a bare SHA-256 are **not** suitable for passwords.

### Using Werkzeug

Werkzeug (installed with Flask) provides ready-made helpers:

```python
from werkzeug.security import generate_password_hash, check_password_hash

hashed = generate_password_hash("correct horse battery staple")
print(hashed)
# example: scrypt:32768:8:1$salt$longhashvalue...

print(check_password_hash(hashed, "correct horse battery staple"))   # True
print(check_password_hash(hashed, "wrong password"))                 # False
```

- The result includes the **algorithm, parameters, and salt**, so you store just one string.
- Use a column long enough for it: `String(256)` is a safe choice.
- Each call to `generate_password_hash` produces a different result even for the same password because the salt is random.

### Other strong options

Libraries such as **argon2-cffi** (Argon2) and **bcrypt** are also widely used. Whichever you choose, use a well-reviewed library and never invent your own scheme.

### Password policy tips

- Require a **minimum length** (8 or more characters; longer is better).
- Allow long passwords and spaces (passphrases).
- Do not require arbitrary complexity rules that frustrate users without adding much safety.
- Consider checking passwords against lists of known breached passwords.
- Rate-limit login attempts (for example with **Flask-Limiter**) to slow down guessing.
- Offer **two-factor authentication** for sensitive apps.

### Changing passwords

```python
@auth_bp.route("/change-password", methods=["POST"])
@login_required
def change_password():
    old = request.form.get("old_password", "")
    new = request.form.get("new_password", "")
    if not current_user.check_password(old):
        flash("Current password is incorrect.", "error")
        return redirect(url_for("main.settings"))
    if len(new) < 8:
        flash("New password must be at least 8 characters.", "error")
        return redirect(url_for("main.settings"))
    current_user.set_password(new)
    db.session.commit()
    flash("Password updated.", "success")
    return redirect(url_for("main.settings"))
```

### Password resets

- Generate a **single-use, time-limited token** (for example with `itsdangerous.URLSafeTimedSerializer`) and email a link containing it.
- Never email the password itself.
- Do not reveal whether an email address has an account in the response message.

---

## 13.4 Protecting Routes

### `login_required`

Add the decorator **below** `@app.route` so Flask registers the protected function:

```python
from flask_login import login_required


@app.route("/dashboard")
@login_required
def dashboard():
    return f"Welcome to your dashboard, {current_user.username}"
```

If an anonymous user visits `/dashboard`, Flask-Login redirects them to `login_manager.login_view` with a `?next=/dashboard` parameter, so they return after logging in.

### Custom behavior for unauthorized users

For JSON APIs, you usually want a `401` response instead of a redirect:

```python
@login_manager.unauthorized_handler
def unauthorized():
    if request.path.startswith("/api/"):
        return {"error": "Authentication required"}, 401
    flash("Please log in to continue.", "info")
    return redirect(url_for("auth.login", next=request.path))
```

### Role-based authorization

Add a role column and a decorator that checks it:

```python
from functools import wraps
from flask import abort


class User(UserMixin, db.Model):
    # ... other columns ...
    role: Mapped[str] = mapped_column(String(20), default="user")


def roles_required(*roles):
    def decorator(view):
        @wraps(view)
        @login_required
        def wrapped(*args, **kwargs):
            if current_user.role not in roles:
                abort(403)
            return view(*args, **kwargs)
        return wrapped
    return decorator


@app.route("/admin")
@roles_required("admin")
def admin_panel():
    return "Admin area"
```

- `401` means "not authenticated" (log in first).
- `403` means "authenticated but not permitted".

### Ownership checks

Logging in is not enough; users should only touch **their own** data:

```python
@app.route("/posts/<int:post_id>/edit", methods=["GET", "POST"])
@login_required
def edit_post(post_id):
    post = db.get_or_404(Post, post_id)
    if post.user_id != current_user.id:
        abort(403)
    # ... edit logic ...
```

Forgetting ownership checks leads to a very common vulnerability called **insecure direct object reference (IDOR)**, where changing an ID in a URL exposes someone else's data.

### Protecting an entire blueprint

```python
@admin_bp.before_request
@login_required
def require_login():
    pass
```

Every route in `admin_bp` now requires login.

### Remember-me and fresh logins

- `remember=True` keeps users logged in after the browser closes.
- Use `@fresh_login_required` for sensitive actions (like changing an email). It requires the user to have logged in during the current session, not just via a remember cookie.

```python
from flask_login import fresh_login_required

@app.route("/account/delete", methods=["POST"])
@fresh_login_required
def delete_account():
    ...
```

### Checklist

- Hash passwords; never log or email them.
- Protect every private route with `login_required`.
- Check ownership and roles, not only login state.
- Validate the `next` redirect target.
- Use HTTPS and secure cookie settings in production.
- Rate-limit login attempts.

---

[Previous](./[12]-Building-JSON-APIs.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[14]-Security-Basics.md)
