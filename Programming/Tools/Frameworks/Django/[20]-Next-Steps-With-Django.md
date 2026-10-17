[Previous](./[19]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Django.md)

*Quality And Delivery*

# Lesson 20 - Next Steps With Django

---

## 20.1 Celery and background tasks

### The problem: slow work inside a request

Django handles each request while the user waits. If a view does something slow, such as sending an email, resizing an image, generating a PDF, or calling a slow external API, the user stares at a loading spinner, and a worker process is tied up the whole time. If the work fails, the user sees an error for something they did not even need to wait for.

The fix is to move slow work into a **background task**: the view quickly records that work is needed and returns immediately, while a separate process does the work later.

### How Celery works

**Celery** is the most widely used task queue for Python. It has three parts:

| Part | Role |
|---|---|
| **Producer** | Your Django code, which sends a task message |
| **Broker** | A message queue that stores tasks until a worker picks them up (Redis or RabbitMQ) |
| **Worker** | A separate process that fetches tasks from the broker and runs them |

```
Django view  --(task message)-->  Broker (Redis)  --->  Celery worker runs the task
     |
     +--> returns a response to the user immediately
```

An optional **result backend** stores each task's return value or status.

### Installation

```bash
pip install celery redis
```

You also need a running Redis server (for example with Docker: `docker run -p 6379:6379 redis:7`).

### Project setup

Create `mysite/celery.py`:

```python
# mysite/celery.py
import os
from celery import Celery

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings.development")

app = Celery("mysite")
app.config_from_object("django.conf:settings", namespace="CELERY")
app.autodiscover_tasks()          # finds tasks.py in every installed app
```

Load it when Django starts:

```python
# mysite/__init__.py
from .celery import app as celery_app

__all__ = ("celery_app",)
```

Add settings (the `CELERY_` prefix matches `namespace="CELERY"`):

```python
# settings.py
CELERY_BROKER_URL = os.environ.get("REDIS_URL", "redis://localhost:6379/0")
CELERY_RESULT_BACKEND = os.environ.get("REDIS_URL", "redis://localhost:6379/0")
CELERY_TASK_SERIALIZER = "json"
CELERY_ACCEPT_CONTENT = ["json"]
CELERY_TIMEZONE = "UTC"
```

### Writing a task

Tasks live in a `tasks.py` file inside an app:

```python
# blog/tasks.py
from celery import shared_task
from django.core.mail import send_mail

from .models import Post


@shared_task
def send_welcome_email(user_email):
    send_mail(
        subject="Welcome!",
        message="Thanks for signing up.",
        from_email=None,                 # uses DEFAULT_FROM_EMAIL
        recipient_list=[user_email],
    )


@shared_task
def count_words(post_id):
    post = Post.objects.get(pk=post_id)
    return len(post.body.split())
```

**Pass simple values (IDs, strings, numbers), not model instances.** Task arguments are serialized to JSON, and an object may have changed by the time the worker runs. Fetch fresh data from the database inside the task.

### Calling a task

```python
from .tasks import send_welcome_email

def signup(request):
    ...
    send_welcome_email.delay(user.email)          # queue it and return immediately
    return redirect("blog:post_list")
```

| Call | Meaning |
|---|---|
| `task.delay(arg1, arg2)` | Shortcut to queue a task |
| `task.apply_async(args=[...], countdown=60)` | Queue with options (run after 60 seconds, set an expiry, choose a queue) |
| `task(arg1, arg2)` | Runs the function normally, in the current process |

### Running a worker

In a separate terminal (with the same environment variables as the web app):

```bash
celery -A mysite worker --loglevel=info
```

If you forget to start a worker, tasks pile up in the broker and nothing happens. This is a very common beginner issue.

### Waiting for results

```python
result = count_words.delay(post.pk)
result.id           # a task ID you can store
result.status       # "PENDING", "STARTED", "SUCCESS", "FAILURE", "RETRY"
result.get(timeout=10)    # blocks until finished; avoid in views
```

Blocking on `.get()` inside a view defeats the purpose of using a background task. Usually you let the task update the database, and the page shows the outcome later.

### Retries and error handling

External services fail. Let Celery retry:

```python
@shared_task(bind=True, max_retries=3, default_retry_delay=30)
def fetch_exchange_rate(self, currency):
    try:
        return call_remote_api(currency)
    except ConnectionError as exc:
        raise self.retry(exc=exc)
```

Also consider `autoretry_for=(ConnectionError,)` with `retry_backoff=True` for automatic exponential backoff.

Make tasks **idempotent**, meaning running them twice produces the same result as running them once. Workers can crash and tasks can be redelivered, so a task that charges a card twice or sends two emails is a bug.

### Running after the transaction commits

If a view creates an object and queues a task in the same database transaction, the worker may run **before** the commit and fail to find the object. Use `on_commit` (Lesson 13):

```python
from django.db import transaction

def create_post(request):
    post = Post.objects.create(...)
    transaction.on_commit(lambda: count_words.delay(post.pk))
```

### Scheduled (periodic) tasks with Celery beat

**Celery beat** is a scheduler that queues tasks at fixed times, like cron:

```python
# settings.py
from celery.schedules import crontab

CELERY_BEAT_SCHEDULE = {
    "clear-expired-sessions-nightly": {
        "task": "blog.tasks.clear_sessions",
        "schedule": crontab(hour=3, minute=0),
    },
    "refresh-stats-every-5-minutes": {
        "task": "blog.tasks.refresh_stats",
        "schedule": 300.0,
    },
}
```

```bash
celery -A mysite beat --loglevel=info
```

Run only **one** beat process, or tasks will be queued twice.

### Deploying Celery

Celery adds processes to run alongside the web server: at least one worker, plus beat if you schedule tasks. With Docker Compose, that means extra services using the same image but a different command (Lesson 19):

```yaml
  worker:
    build: .
    command: celery -A mysite worker --loglevel=info
    env_file: .env
    depends_on: [redis, db]

  beat:
    build: .
    command: celery -A mysite beat --loglevel=info
    env_file: .env
    depends_on: [redis]

  redis:
    image: redis:7
```

### Is Celery always necessary?

Not always. Lighter alternatives exist for simpler needs:

| Option | Good for |
|---|---|
| **Celery** | Large, flexible, production-grade task processing |
| **RQ** or **Dramatiq** | Simpler task queues, also broker-based |
| **Huey** | Small projects needing a lightweight queue |
| **Django's built-in Tasks framework** (introduced in newer Django versions) | Basic background tasks with a pluggable backend |
| **cron + management commands** | Simple scheduled jobs without a queue |

---

## 20.2 Async Django

### Sync vs async

Normally, Django code is **synchronous**: a worker handles one request at a time, and while it waits for a slow database query or an HTTP call, it sits idle. **Asynchronous** code can pause at a waiting point and let the same process work on other requests in the meantime.

Async helps most when a request spends its time **waiting on I/O**: calling several external APIs, streaming responses, or holding many open connections. It does **not** make CPU-heavy code faster, and it adds complexity, so use it where it brings a real benefit.

### Python's async basics

```python
import asyncio

async def fetch_data():                 # "async def" defines a coroutine
    await asyncio.sleep(1)              # "await" pauses here without blocking the process
    return {"ok": True}

async def main():
    # Run two waits at the same time: total time is about 1 second, not 2
    results = await asyncio.gather(fetch_data(), fetch_data())
```

### Async views

Define a view with `async def`:

```python
# blog/views.py
import asyncio
import httpx
from django.http import JsonResponse


async def dashboard(request):
    async with httpx.AsyncClient() as client:
        weather, news = await asyncio.gather(
            client.get("https://api.example.com/weather"),
            client.get("https://api.example.com/news"),
        )
    return JsonResponse({"weather": weather.json(), "news": news.json()})
```

Both requests run concurrently, so the view finishes in roughly the time of the slowest one.

Class-based views can define async handler methods (`async def get(...)`), but all handlers in one view class must be either all sync or all async.

### Running Django asynchronously

To benefit from async views, serve Django with an **ASGI** server (Lesson 19):

```bash
uvicorn mysite.asgi:application --reload
```

If you run an async view under WSGI, Django still works, but it runs the view in an event loop for each request, and you gain no concurrency advantage.

### The async ORM

Django's ORM supports async through **a-prefixed methods** and **async iteration** for the common operations:

```python
post = await Post.objects.aget(pk=1)
exists = await Post.objects.filter(status="published").aexists()
count = await Post.objects.acount()
new_post = await Post.objects.acreate(title="Async", slug="async", body="...")
await post.asave()
await post.adelete()

# Iterating a QuerySet
async for post in Post.objects.filter(status="published"):
    print(post.title)

posts = [p async for p in Post.objects.all()]
```

Rules to remember:

- QuerySet methods that **return a QuerySet** (`filter`, `order_by`, `select_related`) stay as normal synchronous calls because they do not hit the database until evaluated.
- Methods that **execute the query** have `a`-prefixed versions: `aget`, `acreate`, `aget_or_create`, `acount`, `aexists`, `afirst`, `alast`, `aaggregate`, `abulk_create`, `aupdate`, `adelete`.
- Accessing a related object lazily (such as `post.author`) in async code raises `SynchronousOnlyOperation`, because it would trigger a hidden blocking query. Use `select_related()` or `prefetch_related()` first, so the data is already loaded.

```python
posts = [p async for p in Post.objects.select_related("author")]
for p in posts:
    print(p.author.name)             # safe: already loaded
```

Under the hood, the database layer for most backends still runs in a thread pool, so async ORM calls improve concurrency of the **event loop** but are not true database parallelism.

### Calling sync code from async: sync_to_async

Not everything in Django or third-party libraries is async-ready. Wrap blocking code so it runs in a thread instead of freezing the event loop:

```python
from asgiref.sync import sync_to_async

@sync_to_async
def generate_report(user_id):
    ...                              # blocking code, perhaps using older ORM features
    return report

async def report_view(request):
    report = await generate_report(request.user.pk)
    return JsonResponse(report)
```

The reverse tool, `async_to_sync`, lets synchronous code call an async function:

```python
from asgiref.sync import async_to_sync

result = async_to_sync(fetch_data)()
```

### Pitfalls in async code

| Pitfall | Why it matters |
|---|---|
| Calling blocking functions (`time.sleep`, `requests.get`, heavy computation) in an async view | Freezes the whole event loop, harming every request |
| Lazy related-object access | Raises `SynchronousOnlyOperation` |
| Mixing sync and async middleware | Django adapts, but each switch adds overhead; prefer middleware that supports both |
| Forgetting `await` | You get a coroutine object instead of a result |
| Assuming async is automatically faster | Benefits appear only for I/O-bound concurrency |

Use async-friendly libraries such as `httpx` (instead of `requests`) and `aiofiles` inside async views.

### Beyond requests: WebSockets with Channels

Plain Django (even with async views) handles HTTP request/response. For **WebSockets** (live chat, notifications, real-time dashboards), use the **Django Channels** package on top of ASGI:

```bash
pip install channels daphne
```

Channels adds consumers (like views for WebSocket connections) and channel layers (usually Redis) for broadcasting messages between users.

### Choosing between background tasks and async

| Need | Best tool |
|---|---|
| Work the user does not need to wait for, which may be slow or must be retried | Celery or another task queue |
| Make several I/O calls inside one request and combine them | Async view with `asyncio.gather` |
| Real-time two-way communication | Django Channels |
| Heavy CPU work | Background worker processes, not async |

---

## 20.3 HTMX with Django

### The idea

Traditionally, making a page feel dynamic (updating a list without reloading, live search, inline editing) meant writing a lot of JavaScript and a JSON API consumed by a front-end framework such as React or Vue. **HTMX** takes a different approach: your Django views keep returning **HTML**, and HTMX swaps small pieces of it into the page using simple HTML attributes. You get interactive behavior with little or no custom JavaScript, and you keep Django's templates, forms, validation, and authentication.

### Installing HTMX

HTMX is a single JavaScript file. Add it to your base template (Lesson 4):

```html
<script src="https://unpkg.com/htmx.org@2.0.4"></script>
```

For production, download the file and serve it as a static file (Lesson 14), or use a version pinned with a subresource integrity hash.

### How it works

Add attributes to an element, and HTMX sends an AJAX request on an event, then places the returned HTML in a target element.

| Attribute | Meaning |
|---|---|
| `hx-get`, `hx-post`, `hx-put`, `hx-delete` | The HTTP method and URL to request |
| `hx-target` | CSS selector of the element to update |
| `hx-swap` | How to insert the response (`innerHTML`, `outerHTML`, `beforeend`, `afterbegin`, `delete`, ...) |
| `hx-trigger` | The event that starts the request (`click`, `keyup changed delay:300ms`, `load`, `every 5s`) |
| `hx-indicator` | An element to show while the request is running |
| `hx-confirm` | Ask the user to confirm first |
| `hx-push-url` | Update the browser URL and history |

### Example 1: live search

Template for the full page:

```html
{% extends "base.html" %}

{% block content %}
  <input type="search"
         name="q"
         placeholder="Search posts..."
         hx-get="{% url 'blog:post_search' %}"
         hx-trigger="keyup changed delay:300ms"
         hx-target="#results"
         hx-indicator="#spinner">

  <span id="spinner" class="htmx-indicator">Searching...</span>

  <ul id="results">
    {% include "blog/_post_results.html" %}
  </ul>
{% endblock %}
```

The partial template (the fragment returned to HTMX), `blog/_post_results.html`:

```html
{% for post in posts %}
  <li><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></li>
{% empty %}
  <li>No posts found.</li>
{% endfor %}
```

The view returns only the fragment for HTMX requests:

```python
# blog/views.py
from django.shortcuts import render
from .models import Post


def post_search(request):
    query = request.GET.get("q", "")
    posts = Post.objects.filter(title__icontains=query)[:20]
    return render(request, "blog/_post_results.html", {"posts": posts})
```

### Example 2: delete without a page reload

```html
<li id="post-{{ post.pk }}">
  {{ post.title }}
  <button hx-delete="{% url 'blog:post_delete_htmx' post.pk %}"
          hx-confirm="Delete this post?"
          hx-target="#post-{{ post.pk }}"
          hx-swap="outerHTML">
    Delete
  </button>
</li>
```

```python
from django.http import HttpResponse
from django.views.decorators.http import require_http_methods


@require_http_methods(["DELETE"])
def post_delete_htmx(request, pk):
    post = get_object_or_404(Post, pk=pk, author=request.user)
    post.delete()
    return HttpResponse("")          # an empty response replaces the element, removing it
```

### CSRF with HTMX

HTMX requests that change data (`POST`, `PUT`, `DELETE`) need the CSRF token (Lesson 17). The simplest approach is to send it on every request from the `<body>` tag:

```html
<body hx-headers='{"X-CSRFToken": "{{ csrf_token }}"}'>
```

### Example 3: an inline form

```html
<div id="comment-form">
  <form hx-post="{% url 'blog:add_comment' post.pk %}"
        hx-target="#comments"
        hx-swap="beforeend">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Add comment</button>
  </form>
</div>
<div id="comments">
  {% for comment in post.comments.all %}
    {% include "blog/_comment.html" %}
  {% endfor %}
</div>
```

```python
def add_comment(request, pk):
    post = get_object_or_404(Post, pk=pk)
    form = CommentForm(request.POST)
    if form.is_valid():
        comment = form.save(commit=False)
        comment.post = post
        comment.save()
        return render(request, "blog/_comment.html", {"comment": comment})
    # Return the form again with errors; hx-target would need to point at the form for this
    return render(request, "blog/_comment_form.html", {"form": form, "post": post}, status=422)
```

By default HTMX does not swap responses with 4xx or 5xx statuses; configure `htmx.config.responseHandling` or use a different approach (such as returning `200` with the form containing errors) if you want validation errors displayed.

### Detecting HTMX requests

HTMX adds an `HX-Request: true` header to its requests. Use it to serve a full page for normal visits and a fragment for HTMX calls from the same URL:

```python
def post_list(request):
    posts = Post.objects.all()
    if request.headers.get("HX-Request"):
        return render(request, "blog/_post_results.html", {"posts": posts})
    return render(request, "blog/post_list.html", {"posts": posts})
```

The **django-htmx** package adds `request.htmx` and middleware, plus helpers for response headers like `HX-Redirect` and `HX-Trigger`:

```bash
pip install django-htmx
```

```python
MIDDLEWARE = [
    ...
    "django_htmx.middleware.HtmxMiddleware",
]
```

```python
if request.htmx:
    ...
```

### Where HTMX fits

| Good fit | Weaker fit |
|---|---|
| Server-rendered sites that need some dynamic parts | Very rich client-side apps (drawing tools, offline apps) |
| Search boxes, inline editing, infinite scroll, modals, tabs | Heavy client-side state across many components |
| Teams that prefer Python over JavaScript | Public APIs for mobile apps (use DRF, Lesson 15) |

HTMX pairs especially well with Django's template partials (the `{% include %}` approach above), and with **Alpine.js** for tiny bits of client-side behavior such as toggling a menu.

---

## 20.4 Project ideas

The best way to learn Django is to build things. Here are projects arranged by difficulty, with the lessons from this Topic that each one practices. Pick one, start small, and expand step by step. Deploy at least one project to the internet (Lesson 19), because deployment teaches things that development never does.

### Beginner projects

| Project | What you build | Concepts practiced |
|---|---|---|
| **Personal blog** | Posts, categories, and a detail page; write in the admin | Models, views, templates, URLs, admin (Lessons 3 to 9) |
| **To-do list** | Add, complete, and delete tasks | Forms, ModelForms, CRUD views (Lessons 10, 11) |
| **Polls app** | Questions with choices and vote counting | Relationships, queries, aggregation (Lessons 7, 8) |
| **Portfolio site** | Projects, skills, a contact form that sends email | Templates, static files, forms (Lessons 4, 10, 14) |
| **URL shortener** | Map short codes to long URLs and count clicks | Models, redirects, `F()` expressions (Lessons 3, 7) |

### Intermediate projects

| Project | What you build | Concepts practiced |
|---|---|---|
| **Blog with accounts** | Registration, login, authors editing only their own posts, comments | Authentication, permissions, class-based views (Lessons 11, 12) |
| **Bookmark manager** | Users save links with tags, search and filter | Many-to-many, Q objects, pagination (Lessons 7, 8, 11) |
| **Recipe book** | Recipes, ingredients, image uploads, favorites | Media files, relationships, forms (Lessons 8, 10, 14) |
| **Expense tracker** | Record spending by category with monthly totals and charts | Aggregation, annotation, custom model methods (Lessons 5, 7) |
| **Library or inventory system** | Books or items, borrowing, due dates, staff-only admin | Models, admin customization, permissions (Lessons 5, 9, 12) |
| **REST API for any of the above** | JSON endpoints with token authentication | Django REST Framework (Lesson 15) |

### Advanced projects

| Project | What you build | Concepts practiced |
|---|---|---|
| **E-commerce store** | Products, cart in the session, orders, payment integration | Sessions, signals, transactions, security (Lessons 13, 17) |
| **Task manager with teams** | Projects, assignments, roles, notifications by email | Custom user model, permissions, Celery (Lessons 12, 20) |
| **Real-time chat or notifications** | Live messages between users | Async Django, Channels, WebSockets (Lesson 20) |
| **Social network** | Profiles, follow system, feed, likes | Many-to-many with `through`, query optimization, caching (Lessons 7, 8, 13) |
| **SaaS starter** | Multi-tenant accounts, subscriptions, an API, a dashboard | Everything: auth, DRF, testing, settings, deployment |
| **Job board with search** | Listings, applications, full-text search, resume uploads | Querying, file uploads, PostgreSQL search, background email |
| **HTMX-powered dashboard** | A dynamic admin-style interface without a JavaScript framework | HTMX, class-based views, partial templates (Lessons 11, 20) |

### How to approach a project

1. **Describe it in a few sentences.** What can a user do?
2. **Sketch the data.** List the models and how they relate (Lesson 8).
3. **Start with one app** and the smallest working feature. Get a page showing real data first.
4. **Use the admin** to add test data before you build forms (Lesson 9).
5. **Add features one at a time**, committing to Git frequently.
6. **Write tests** for important behavior as you go (Lesson 16).
7. **Handle settings and secrets** properly from the start (Lessons 17, 18).
8. **Deploy early**, even a rough version, and keep deploying as you improve it (Lesson 19).

### Habits that make you a stronger Django developer

- Read the **official documentation** at `docs.djangoproject.com`; it is among the best of any framework, and its tutorial and topic guides are excellent.
- Read other people's code: browse well-known open-source Django projects and packages.
- Learn your tools: the Django shell, `django-debug-toolbar`, and the debugger.
- Keep Django and dependencies up to date, and read release notes.
- Practice SQL basics; understanding what the ORM generates makes you better at both.
- Contribute: report bugs, improve documentation, or write a small open-source package.

### Where to go next

| Direction | Topics to explore |
|---|---|
| **Front end** | HTMX, Alpine.js, Tailwind CSS, or a React/Vue front end talking to DRF |
| **Back end depth** | PostgreSQL features (full-text search, JSONB, indexes), caching strategies, query optimization |
| **Architecture** | Service layers, domain-driven design, modular monoliths, clean project structure |
| **APIs** | GraphQL with Graphene or Strawberry, OpenAPI documentation with `drf-spectacular` |
| **Operations** | Docker, Kubernetes, monitoring, observability, infrastructure as code |
| **Quality** | pytest, property-based testing, type hints with `django-stubs` and mypy |
| **Third-party packages** | `django-allauth` (social login), `django-filter`, `django-crispy-forms`, `wagtail` (CMS), `django-cms` |

Congratulations on completing this Topic. You now have the knowledge to design, build, secure, test, and deploy a complete Django web application. Choose a project, start building, and keep learning.

---

[Previous](./[19]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Django.md)
