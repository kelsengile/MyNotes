[Previous](./[11]-Class-Based-Views.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[13]-Middleware-Sessions-And-Caching.md)

*Building Features*

# Lesson 12 - Authentication And Permissions

---

## 12.1 User model and login

Django ships with a complete **authentication system** (`django.contrib.auth`). It handles users, passwords, sessions for logged-in users, groups, and permissions. Two ideas are worth separating:

- **Authentication** answers "**who** are you?" (logging in).
- **Authorization** answers "**what** are you allowed to do?" (permissions).

### What is already set up

A new project already has the required apps and middleware:

```python
INSTALLED_APPS = [
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    ...
]

MIDDLEWARE = [
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    ...
]
```

`AuthenticationMiddleware` adds `request.user` to every request. It is either a real `User` or an `AnonymousUser`.

### The User model

The built-in `User` model has these main fields:

| Field | Description |
|---|---|
| `username` | Unique login name (required) |
| `password` | Stored as a **hash**, never plain text |
| `email`, `first_name`, `last_name` | Optional personal details |
| `is_active` | If `False`, the user cannot log in |
| `is_staff` | Can log into the admin site |
| `is_superuser` | Has every permission |
| `date_joined`, `last_login` | Timestamps |

### Creating users

Never create users with `User.objects.create()` and a plain password, because that would store the password unhashed. Use `create_user()`:

```python
from django.contrib.auth import get_user_model

User = get_user_model()

user = User.objects.create_user(username="ada", email="ada@example.com", password="s3cret-pass")
admin = User.objects.create_superuser(username="root", email="root@example.com", password="...")
```

### Checking who is logged in

```python
def profile(request):
    if request.user.is_authenticated:
        return HttpResponse(f"Hello, {request.user.username}")
    return HttpResponse("Hello, stranger")
```

In templates, the `user` variable is provided by the `auth` context processor:

```html
{% if user.is_authenticated %}
  <p>Signed in as {{ user.username }}.</p>
{% else %}
  <p><a href="{% url 'login' %}">Log in</a></p>
{% endif %}
```

### Logging in and out manually

```python
from django.contrib.auth import authenticate, login, logout


def my_login(request):
    if request.method == "POST":
        user = authenticate(
            request,
            username=request.POST["username"],
            password=request.POST["password"],
        )
        if user is not None:
            login(request, user)         # creates the session
            return redirect("blog:post_list")
        error = "Invalid username or password."
    ...


def my_logout(request):
    logout(request)                      # clears the session
    return redirect("login")
```

`authenticate()` returns the `User` if the credentials are valid, otherwise `None`. `login()` attaches the user to the session. Your logout view should accept only `POST` requests, because a `GET` that changes state is unsafe.

### Built-in login and logout views

You rarely need to write these yourself. Include Django's ready-made views:

```python
# mysite/urls.py
from django.urls import include, path

urlpatterns = [
    path("accounts/", include("django.contrib.auth.urls")),
    ...
]
```

This provides these named URLs: `login`, `logout`, `password_change`, `password_change_done`, `password_reset`, `password_reset_done`, `password_reset_confirm`, and `password_reset_complete`.

Django does **not** supply the templates (except for the admin's). Create `templates/registration/login.html`:

```html
{% extends "base.html" %}

{% block content %}
  <h1>Log in</h1>
  <form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="hidden" name="next" value="{{ next }}">
    <button type="submit">Log in</button>
  </form>
{% endblock %}
```

Logging out must be done with a `POST` request in current Django versions:

```html
<form method="post" action="{% url 'logout' %}">
  {% csrf_token %}
  <button type="submit">Log out</button>
</form>
```

### Settings for redirects

```python
LOGIN_URL = "login"                    # where anonymous users are sent
LOGIN_REDIRECT_URL = "blog:post_list"  # where to go after a successful login
LOGOUT_REDIRECT_URL = "login"          # where to go after logging out
```

### Requiring login

For function-based views, use the `login_required` decorator:

```python
from django.contrib.auth.decorators import login_required


@login_required
def dashboard(request):
    ...
```

Anonymous visitors are redirected to `LOGIN_URL` with a `?next=/original/path/` parameter, so they return to the page they wanted after logging in. For class-based views use `LoginRequiredMixin` (Lesson 11).

### Registration (sign-up)

Django has `UserCreationForm` but no ready-made sign-up view, so you write a small one:

```python
from django.contrib.auth import login
from django.contrib.auth.forms import UserCreationForm
from django.shortcuts import redirect, render


def signup(request):
    if request.method == "POST":
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect("blog:post_list")
    else:
        form = UserCreationForm()
    return render(request, "registration/signup.html", {"form": form})
```

---

## 12.2 Custom user models

### Why customize?

The default `User` uses a username and has fixed fields. Many projects want to log in with an **email address** or add fields like a phone number or avatar. Django strongly recommends that you **always start a new project with a custom user model**, even if it is identical to the default, because changing the user model after the first migrations are applied is very painful.

### Option 1: AbstractUser (the easy way)

Subclass `AbstractUser` to keep everything Django provides and add your own fields:

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser
from django.db import models


class User(AbstractUser):
    phone = models.CharField(max_length=20, blank=True)
    avatar = models.ImageField(upload_to="avatars/", blank=True)
```

Then tell Django to use it **before running your first migration**:

```python
# settings.py
AUTH_USER_MODEL = "accounts.User"
```

Register it in the admin using Django's `UserAdmin` so passwords are handled correctly:

```python
# accounts/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User


@admin.register(User)
class CustomUserAdmin(UserAdmin):
    fieldsets = UserAdmin.fieldsets + (
        ("Extra info", {"fields": ("phone", "avatar")}),
    )
```

### Referencing the user model

Never import `User` from `django.contrib.auth.models` in a project with a custom user. Use these instead:

```python
# In models, for relations:
from django.conf import settings

author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)

# In other code (views, forms, services):
from django.contrib.auth import get_user_model

User = get_user_model()
```

### Option 2: Email as the username

To log in with an email address, keep `AbstractUser` but replace the username with `email`, and provide a custom manager:

```python
from django.contrib.auth.base_user import BaseUserManager
from django.contrib.auth.models import AbstractUser
from django.db import models


class UserManager(BaseUserManager):
    use_in_migrations = True

    def create_user(self, email, password=None, **extra_fields):
        if not email:
            raise ValueError("An email address is required.")
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, password=None, **extra_fields):
        extra_fields.setdefault("is_staff", True)
        extra_fields.setdefault("is_superuser", True)
        return self.create_user(email, password, **extra_fields)


class User(AbstractUser):
    username = None
    email = models.EmailField(unique=True)

    USERNAME_FIELD = "email"
    REQUIRED_FIELDS = []          # fields asked for by createsuperuser, besides email and password

    objects = UserManager()

    def __str__(self):
        return self.email
```

### Option 3: AbstractBaseUser (full control)

`AbstractBaseUser` provides only the password and login-tracking machinery. You define everything else, including permissions support (via `PermissionsMixin`). It is rarely needed, and `AbstractUser` is the right choice for nearly every project.

### Profile model alternative

If you cannot change the user model (for example, in an existing project or a reusable app), put extra data in a separate model with a `OneToOneField` (Lesson 8):

```python
class Profile(models.Model):
    user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
```

### Quick checklist

- Create the custom user model **before** the first `migrate`.
- Set `AUTH_USER_MODEL` in settings.
- Use `get_user_model()` or `settings.AUTH_USER_MODEL` everywhere instead of importing `User` directly.
- Register it with the admin using `UserAdmin` as the base class.

---

## 12.3 Permissions and groups

### Permissions

A **permission** is a named right to perform an action. For every model, Django automatically creates four permissions when you run `migrate`:

| Permission codename | Example | Meaning |
|---|---|---|
| `add_<model>` | `blog.add_post` | May create objects |
| `change_<model>` | `blog.change_post` | May edit objects |
| `delete_<model>` | `blog.delete_post` | May delete objects |
| `view_<model>` | `blog.view_post` | May view objects |

Permissions are written as `<app_label>.<codename>`.

### Checking permissions

```python
user.has_perm("blog.add_post")                     # True or False
user.has_perms(["blog.add_post", "blog.change_post"])   # needs all of them
user.get_all_permissions()                         # set of "app.codename" strings
```

Superusers pass every check. Inactive users pass none.

In templates, use the `perms` variable:

```html
{% if perms.blog.add_post %}
  <a href="{% url 'blog:post_create' %}">Write a post</a>
{% endif %}
```

### Enforcing permissions in views

```python
from django.contrib.auth.decorators import permission_required


@permission_required("blog.delete_post", raise_exception=True)    # 403 instead of redirect
def delete_post(request, pk):
    ...
```

For class-based views: `PermissionRequiredMixin` (Lesson 11).

### Assigning permissions to a user

```python
from django.contrib.auth.models import Permission

perm = Permission.objects.get(codename="add_post")
user.user_permissions.add(perm)
user.user_permissions.remove(perm)
```

Permission checks are cached on the user object, so re-fetch the user (or call `get_object_or_404` again) after changing permissions inside the same request.

### Custom permissions

Add your own in the model's `Meta` (Lesson 5), then run `makemigrations` and `migrate`:

```python
class Post(models.Model):
    ...

    class Meta:
        permissions = [
            ("publish_post", "Can publish a post"),
            ("feature_post", "Can feature a post on the homepage"),
        ]
```

```python
if request.user.has_perm("blog.publish_post"):
    ...
```

### Groups

A **group** is a named set of permissions. Assign users to groups rather than assigning permissions one by one. A user's effective permissions are those from their own list **plus** those from all their groups.

```python
from django.contrib.auth.models import Group, Permission

editors = Group.objects.create(name="Editors")
editors.permissions.add(
    Permission.objects.get(codename="add_post"),
    Permission.objects.get(codename="change_post"),
)

user.groups.add(editors)
user.groups.remove(editors)

user.groups.filter(name="Editors").exists()      # is the user an editor?
```

You can also create groups and assign permissions in the admin interface, which is the usual way to manage them.

### Object-level permissions

Django's built-in permissions are **model-level**: "may this user edit *any* post?". They cannot say "may edit *this particular* post". For that, write explicit checks (such as `post.author == request.user`, see `UserPassesTestMixin` in Lesson 11) or use a package like `django-guardian`.

### Summary of access levels

| Check | Meaning |
|---|---|
| `is_authenticated` | The user is logged in |
| `is_active` | The account is enabled |
| `is_staff` | May use the admin site (not necessarily do anything in it) |
| `is_superuser` | Has all permissions |
| `has_perm(...)` | Has a specific permission, directly or via a group |

---

## 12.4 Password management

### How passwords are stored

Django **never stores passwords**. It stores a **hash**: a one-way fingerprint produced by a slow, salted algorithm. The stored value looks like:

```
pbkdf2_sha256$870000$Zs4k...$wn3Rd...
```

It contains the algorithm, the number of iterations, a random salt, and the hash. The default algorithm is **PBKDF2** with SHA-256. Django upgrades old hashes to the current settings automatically the next time a user logs in.

### Working with passwords in code

```python
user.set_password("new-password")     # hashes it; you must call save() afterwards
user.save()

user.check_password("new-password")   # True or False

user.set_unusable_password()          # the user cannot log in with a password
```

Do not write `user.password = "plain"`; the value would be treated as a hash and the user could never log in.

### Choosing hashers

```python
# settings.py (the default order, with PBKDF2 first)
PASSWORD_HASHERS = [
    "django.contrib.auth.hashers.PBKDF2PasswordHasher",
    "django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher",
    "django.contrib.auth.hashers.Argon2PasswordHasher",
    "django.contrib.auth.hashers.BCryptSHA256PasswordHasher",
]
```

The **first** hasher is used for new passwords; the rest are still accepted for verification. To use Argon2 (a modern algorithm), install it with `pip install "django[argon2]"` and put `Argon2PasswordHasher` first.

### Password validators

Django can reject weak passwords. The default project template enables these:

```python
AUTH_PASSWORD_VALIDATORS = [
    {"NAME": "django.contrib.auth.password_validation.UserAttributeSimilarityValidator"},
    {"NAME": "django.contrib.auth.password_validation.MinimumLengthValidator"},
    {"NAME": "django.contrib.auth.password_validation.CommonPasswordValidator"},
    {"NAME": "django.contrib.auth.password_validation.NumericPasswordValidator"},
]
```

| Validator | Rejects |
|---|---|
| `UserAttributeSimilarityValidator` | Passwords too similar to the username, name, or email |
| `MinimumLengthValidator` | Passwords shorter than 8 characters (configurable) |
| `CommonPasswordValidator` | Passwords on a list of 20,000 common ones |
| `NumericPasswordValidator` | Entirely numeric passwords |

Configure options with `"OPTIONS": {"min_length": 12}`. Validators run in forms such as `UserCreationForm` and `PasswordChangeForm`, but **not** in `create_user()`; call `validate_password()` yourself if you need it:

```python
from django.contrib.auth.password_validation import validate_password

validate_password("abc123", user=user)     # raises ValidationError if weak
```

### Changing a password

The built-in `PasswordChangeView` (included with `django.contrib.auth.urls`) handles this for logged-in users. It needs two templates:

- `registration/password_change_form.html`
- `registration/password_change_done.html`

If you change a password yourself, call `update_session_auth_hash()` so the user is not logged out:

```python
from django.contrib.auth import update_session_auth_hash

user.set_password(new_password)
user.save()
update_session_auth_hash(request, user)
```

### Resetting a forgotten password

Include `django.contrib.auth.urls`, and Django provides the full flow:

1. `password_reset`: the user enters their email.
2. `password_reset_done`: "we sent you an email".
3. `password_reset_confirm`: the user follows the emailed link and sets a new password.
4. `password_reset_complete`: success page.

Templates needed (all in `templates/registration/`): `password_reset_form.html`, `password_reset_done.html`, `password_reset_email.html`, `password_reset_confirm.html`, and `password_reset_complete.html`.

The email link contains a token that is valid for `PASSWORD_RESET_TIMEOUT` seconds (3 days by default) and becomes invalid once the password changes.

### Sending email

During development, print emails to the terminal instead of sending them:

```python
# settings.py
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
```

In production, configure SMTP:

```python
EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
EMAIL_HOST = "smtp.example.com"
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = os.environ["EMAIL_USER"]
EMAIL_HOST_PASSWORD = os.environ["EMAIL_PASSWORD"]
DEFAULT_FROM_EMAIL = "noreply@example.com"
```

Keep credentials in environment variables, never in the code (Lesson 17, Lesson 18).

### Password security reminders

- Serve login pages **only over HTTPS** in production (Lesson 17).
- Do not reveal whether an email address exists in password-reset responses beyond what Django already does.
- Limit login attempts with `django-axes` or similar to defeat brute-force attacks.
- Encourage long passphrases rather than complex short passwords.
- Never log, print, or email a user's password.

---

[Previous](./[11]-Class-Based-Views.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[13]-Middleware-Sessions-And-Caching.md)
