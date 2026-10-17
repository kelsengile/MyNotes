[Previous](./[12]-Authentication-And-Permissions.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[14]-Static-And-Media-Files.md)

*Building Features*

# Lesson 13 - Middleware, Sessions, And Caching

---

## 13.1 Middleware

**Middleware** is a chain of small components that sit between the web server and your views. Every request passes through each middleware on the way **in**, and every response passes back through them in reverse order on the way **out**. Middleware is how Django implements cross-cutting features such as sessions, authentication, CSRF protection, and security headers.

### Where middleware is configured

```python
# settings.py
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]
```

### Order matters

Think of the list as layers of an onion:

```
Request  ->  Security -> Sessions -> Common -> CSRF -> Auth -> Messages -> ... -> View
Response <-  Security <- Sessions <- Common <- CSRF <- Auth <- Messages <- ... <- View
```

- On the way in, middleware runs **top to bottom**.
- On the way out, it runs **bottom to top**.
- A middleware may depend on one above it. For example, `AuthenticationMiddleware` needs `SessionMiddleware` to come first because it reads the user from the session.

### What the default middleware does

| Middleware | Purpose |
|---|---|
| `SecurityMiddleware` | HTTPS redirects, HSTS, and other security headers (Lesson 17) |
| `SessionMiddleware` | Loads and saves `request.session` |
| `CommonMiddleware` | URL normalization (such as adding a trailing slash) and other small fixes |
| `CsrfViewMiddleware` | Protects POST forms against CSRF (Lesson 17) |
| `AuthenticationMiddleware` | Adds `request.user` |
| `MessageMiddleware` | Supports one-time flash messages |
| `XFrameOptionsMiddleware` | Adds the `X-Frame-Options` header to prevent clickjacking |

### Writing your own middleware (function style)

A middleware is a callable that receives `get_response` (the next layer) and returns another callable that handles a request:

```python
# blog/middleware.py
import time


def timing_middleware(get_response):
    # One-time setup code runs here, when the server starts.

    def middleware(request):
        # Code here runs BEFORE the view (and later middleware).
        start = time.perf_counter()

        response = get_response(request)     # call the next layer / the view

        # Code here runs AFTER the view.
        duration = time.perf_counter() - start
        response["X-Request-Duration"] = f"{duration:.4f}s"
        return response

    return middleware
```

### Writing your own middleware (class style)

```python
class SimpleLoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response      # runs once at startup

    def __call__(self, request):              # runs for every request
        print(f"{request.method} {request.path}")
        response = self.get_response(request)
        print(f"-> {response.status_code}")
        return response
```

### Activating it

Add the dotted path to `MIDDLEWARE`, placing it where it makes sense in the chain:

```python
MIDDLEWARE = [
    ...
    "blog.middleware.SimpleLoggingMiddleware",
]
```

### Short-circuiting

A middleware can return a response **without** calling the rest of the chain, which is useful for blocking requests:

```python
from django.http import HttpResponseForbidden


class BlockBadBotsMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        agent = request.headers.get("User-Agent", "")
        if "BadBot" in agent:
            return HttpResponseForbidden("Not allowed")     # the view never runs
        return self.get_response(request)
```

### Optional hook methods

Class-based middleware may also define these methods:

| Method | When it runs |
|---|---|
| `process_view(request, view_func, view_args, view_kwargs)` | Just before Django calls the view |
| `process_exception(request, exception)` | When the view raises an exception |
| `process_template_response(request, response)` | When the response has a `render()` method (such as `TemplateResponse`) |

### Middleware best practices

- Keep middleware **fast**: it runs on every request.
- Do not put logic here that belongs to a single view.
- Prefer decorators or mixins for behavior that applies to only some views.
- Watch the order, and test after reordering.

---

## 13.2 Sessions and cookies

HTTP is **stateless**: each request is independent, and the server does not remember who you are. **Cookies** and **sessions** add memory on top of that.

- A **cookie** is a small piece of data the server asks the browser to store and send back on later requests.
- A **session** keeps data **on the server** and gives the browser only a random **session ID** in a cookie (named `sessionid` by default).

Because the real data stays on the server, sessions are safer than storing information in cookies directly.

### Using sessions

With `SessionMiddleware` enabled, every view has `request.session`, which works like a dictionary:

```python
def add_to_cart(request, product_id):
    cart = request.session.get("cart", [])
    cart.append(product_id)
    request.session["cart"] = cart          # assigning marks the session as changed
    return redirect("shop:cart")
```

Common operations:

```python
request.session["key"] = "value"          # set
request.session.get("key", "default")     # read safely
"key" in request.session                  # test
del request.session["key"]                # delete one key
request.session.pop("key", None)          # delete and return

request.session.flush()                   # delete all data and the session itself (used by logout)
request.session.set_expiry(300)           # expire in 300 seconds
request.session.set_expiry(0)             # expire when the browser closes
```

**Important:** Django only detects changes made by **assigning** to a key. If you modify a mutable value in place, set `request.session.modified = True`:

```python
request.session["cart"].append(5)         # in-place change is not detected!
request.session.modified = True           # tell Django to save it
```

Session values must be **JSON-serializable** (strings, numbers, lists, dictionaries), not model instances.

### Session storage backends

```python
# settings.py
SESSION_ENGINE = "django.contrib.sessions.backends.db"
```

| Backend | Where data lives | Notes |
|---|---|---|
| `...backends.db` | Database table `django_session` | Default; reliable |
| `...backends.cache` | Cache (e.g. Redis) | Fast, but lost if the cache is cleared |
| `...backends.cached_db` | Cache plus database | Fast reads with durable storage |
| `...backends.file` | Files on disk | Simple setups |
| `...backends.signed_cookies` | In the cookie itself (signed) | No server storage; the user can read it, and size is limited to about 4 KB |

Expired sessions in the database table are not removed automatically. Clean them periodically:

```bash
python manage.py clearsessions
```

### Session settings

| Setting | Meaning |
|---|---|
| `SESSION_COOKIE_AGE` | Lifetime in seconds (default 2 weeks) |
| `SESSION_EXPIRE_AT_BROWSER_CLOSE` | End the session when the browser closes |
| `SESSION_COOKIE_SECURE` | Send the cookie only over HTTPS (set `True` in production) |
| `SESSION_COOKIE_HTTPONLY` | Hide the cookie from JavaScript (default `True`) |
| `SESSION_COOKIE_SAMESITE` | Cross-site sending policy (`"Lax"` by default) |
| `SESSION_SAVE_EVERY_REQUEST` | Extend the expiry on every request |

### Working with cookies directly

Sometimes you want a plain cookie, such as a theme preference that is not sensitive:

```python
def set_theme(request):
    response = redirect("blog:post_list")
    response.set_cookie("theme", "dark", max_age=60 * 60 * 24 * 365, samesite="Lax")
    return response


def read_theme(request):
    theme = request.COOKIES.get("theme", "light")
    ...


def clear_theme(request):
    response = redirect("blog:post_list")
    response.delete_cookie("theme")
    return response
```

Useful `set_cookie` arguments: `max_age` (seconds), `expires`, `secure`, `httponly`, `samesite`, `path`, and `domain`.

For tamper-proof cookies, use signed cookies:

```python
response.set_signed_cookie("visited", "yes")
request.get_signed_cookie("visited", default=None)
```

A signed cookie is protected from modification but is **not encrypted**, so users can still read it.

### Cookie and session security

- Never store passwords, secrets, or personal data in plain cookies.
- Keep `SESSION_COOKIE_HTTPONLY = True` so JavaScript cannot steal the session ID.
- Use `SESSION_COOKIE_SECURE = True` and HTTPS in production.
- Call `logout()` or `session.flush()` on logout so the session ID cannot be reused.

---

## 13.3 Cache framework

Some work is slow: heavy database queries, remote API calls, rendering large pages. **Caching** stores the result of expensive work so later requests can reuse it instantly.

### Configuring a cache

```python
# settings.py
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.locmem.LocMemCache",
        "LOCATION": "unique-snowflake",
    }
}
```

Available backends:

| Backend | Use for |
|---|---|
| `locmem.LocMemCache` | Development and tests; memory of one process only |
| `redis.RedisCache` | Production; shared and fast (needs `pip install redis`) |
| `memcached.PyMemcacheCache` | Production; shared and fast |
| `db.DatabaseCache` | When no cache server is available (run `createcachetable` first) |
| `filebased.FileBasedCache` | Simple, single-server setups |
| `dummy.DummyCache` | Caching "turned off" while keeping the code in place |

Redis example:

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.redis.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}
```

### The low-level cache API

```python
from django.core.cache import cache

cache.set("top_posts", posts_list, timeout=300)      # keep for 300 seconds
cache.get("top_posts")                                # None if missing or expired
cache.get("top_posts", [])                            # custom default
cache.delete("top_posts")
cache.get_or_set("top_posts", compute_top_posts, 300) # fetch, or compute and store
cache.incr("page_hits")                               # atomic counter (key must exist)
cache.clear()                                         # remove EVERYTHING in the cache
```

Typical pattern:

```python
def top_posts():
    posts = cache.get("top_posts")
    if posts is None:
        posts = list(Post.objects.order_by("-views")[:10])    # evaluate the QuerySet!
        cache.set("top_posts", posts, 300)
    return posts
```

Remember to turn a QuerySet into a `list` before caching it, otherwise you cache an unevaluated query.

### Caching whole views

```python
from django.views.decorators.cache import cache_page


@cache_page(60 * 15)             # 15 minutes
def post_list(request):
    ...
```

For class-based views, wrap the URL:

```python
path("", cache_page(60 * 15)(views.PostListView.as_view()), name="post_list")
```

Caching entire pages is risky when a page differs per user, because one user's version could be shown to another. Use `vary_on_cookie` or avoid it for personalized pages.

### Caching template fragments

Cache just an expensive part of a template:

```html
{% load cache %}

{% cache 600 sidebar request.user.pk %}
  ... expensive sidebar markup ...
{% endcache %}
```

The extra arguments (`request.user.pk`) make a separate cached copy per value.

### Cache keys and invalidation

The hard part of caching is knowing when to **throw data away**. Options:

- **Time-based:** a short timeout, accepting slightly stale data.
- **Event-based:** delete the key when the data changes, often with a signal (Section 13.4):

```python
from django.core.cache import cache
from django.db.models.signals import post_delete, post_save
from django.dispatch import receiver


@receiver([post_save, post_delete], sender=Post)
def clear_post_cache(sender, **kwargs):
    cache.delete("top_posts")
```

- **Versioned keys:** include a version number or timestamp in the key.

### Caching guidelines

- Cache only data that is **expensive** to produce and **safe** to share between users.
- Never cache pages containing private data without keying by user.
- Always handle a cache miss: the cache may be empty at any moment.
- Optimize queries first (`select_related`, `prefetch_related`, indexes; Lesson 7). A cache should not hide a slow query.
- Measure the benefit; do not cache by habit.

---

## 13.4 Signals

**Signals** let one part of your code announce that something happened, and let other parts react without the sender knowing about them. This keeps components **loosely coupled**.

### Built-in signals

| Signal | Sent when |
|---|---|
| `pre_save` / `post_save` | Before / after a model's `save()` |
| `pre_delete` / `post_delete` | Before / after a model instance is deleted |
| `m2m_changed` | A many-to-many relation changes |
| `request_started` / `request_finished` | A request begins / ends |
| `user_logged_in` / `user_logged_out` / `user_login_failed` | Authentication events |
| `pre_migrate` / `post_migrate` | Around the `migrate` command |

### Receiving a signal

Write a function and connect it with the `@receiver` decorator:

```python
# blog/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.conf import settings

from .models import Profile


@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```

A `post_save` receiver gets:

- `sender`: the model class.
- `instance`: the object that was saved.
- `created`: `True` if a new row was inserted, `False` for an update.
- `**kwargs`: always accept extra keyword arguments.

### Connecting signals in apps.py

A signal handler only works if its module is **imported**. The standard place to do that is the `ready()` method of the app configuration (Lesson 2):

```python
# blog/apps.py
from django.apps import AppConfig


class BlogConfig(AppConfig):
    default_auto_field = "django.db.models.BigAutoField"
    name = "blog"

    def ready(self):
        from . import signals      # noqa: F401  (importing registers the receivers)
```

Make sure `INSTALLED_APPS` lists the app (either `"blog"` or `"blog.apps.BlogConfig"`).

### Custom signals

Define your own for events specific to your project:

```python
# blog/signals.py
from django.dispatch import Signal

post_published = Signal()          # providing_args is no longer needed


# Sending, from somewhere in your code:
post_published.send(sender=Post, post=post)


# Receiving:
@receiver(post_published)
def notify_subscribers(sender, post, **kwargs):
    ...
```

### Limits and cautions

- `post_save` is **not** sent by `QuerySet.update()`, `bulk_create()`, or `bulk_update()`, and `post_delete` is not sent by raw SQL. Do not rely on signals for rules that must always hold.
- Signals make control flow **implicit**: someone reading `post.save()` cannot see that other code runs. Overusing them makes programs hard to follow and debug.
- Signal handlers run **synchronously** and delay the request. For slow work (sending email, calling APIs), queue a background task instead (Lesson 20).
- Errors in a receiver can break the original `save()` unless handled.
- Use `transaction.on_commit()` when the handler should run only after the database transaction succeeds:

```python
from django.db import transaction


@receiver(post_save, sender=Post)
def after_post_saved(sender, instance, created, **kwargs):
    if created:
        transaction.on_commit(lambda: send_new_post_email(instance.pk))
```

### Alternatives to signals

Before reaching for a signal, consider simpler options:

- Override the model's `save()` method for logic tied to one model.
- Put the logic in a **service function** that your views call explicitly.
- Use a form's or a view's `form_valid()` method (Lesson 11).

Signals are best when you must react to events from code you do not control, such as Django's own `User` model or a third-party app.

---

[Previous](./[12]-Authentication-And-Permissions.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[14]-Static-And-Media-Files.md)
