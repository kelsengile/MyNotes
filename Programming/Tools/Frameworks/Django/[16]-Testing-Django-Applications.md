[Previous](./[15]-Django-REST-Framework.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[17]-Security-Best-Practices.md)

*Quality And Delivery*

# Lesson 16 - Testing Django Applications

---

## 16.1 Test client

### Why write tests?

**Automated tests** are programs that check that your code behaves correctly. They let you change code with confidence, because a failing test tells you immediately when something broke. Without tests, every change risks silently breaking a feature you forgot about.

Django builds on Python's standard `unittest` module and adds tools for web applications: a test **database**, a fake **web browser** (the test client), and many helpful assertions.

### Where tests live

Each app has a `tests.py` file created by `startapp`. As an app grows, replace it with a `tests/` package:

```
blog/
└── tests/
    ├── __init__.py
    ├── test_models.py
    ├── test_views.py
    └── test_forms.py
```

Django discovers files named `test*.py` and test methods whose names start with `test`.

### Running tests

```bash
python manage.py test                       # all tests in the project
python manage.py test blog                  # one app
python manage.py test blog.tests.test_views # one module
python manage.py test blog.tests.test_views.PostListTests.test_list_page_loads   # one method
python manage.py test --keepdb              # reuse the test database between runs (faster)
python manage.py test --parallel            # use several CPU cores
python manage.py test -v 2                  # more verbose output
```

### The test database

Tests **never** touch your real database. Django creates a separate, empty database (named with a `test_` prefix, or in memory for SQLite), applies all migrations, runs the tests, and destroys it afterwards. Each test starts from a clean state.

### A first test

```python
# blog/tests/test_views.py
from django.test import TestCase
from django.urls import reverse


class HomePageTests(TestCase):
    def test_post_list_page_loads(self):
        response = self.client.get(reverse("blog:post_list"))
        self.assertEqual(response.status_code, 200)
```

`django.test.TestCase` provides `self.client`, an instance of the **test client**.

### The test client

The test client acts like a browser that talks directly to your Django code, with no real server or network involved:

```python
response = self.client.get("/blog/")
response = self.client.get("/blog/", {"q": "django"})                # query string
response = self.client.post("/blog/new/", {"title": "Hi", "body": "x"})   # form data
response = self.client.post("/api/posts/", {"title": "Hi"}, content_type="application/json")
response = self.client.put(...)
response = self.client.delete(...)
```

Using `reverse()` instead of hard-coded URLs keeps tests working when URLs change (Lesson 3).

### Inspecting the response

| Attribute | Description |
|---|---|
| `response.status_code` | The HTTP status |
| `response.content` | The raw bytes of the body |
| `response.context` | The template context (a `dict`-like; read `response.context["posts"]`) |
| `response.templates` | The templates that were rendered |
| `response.url` | The redirect target, for redirect responses |
| `response.json()` | Parsed JSON body |

### Useful response assertions

```python
self.assertEqual(response.status_code, 200)
self.assertContains(response, "Hello, World")            # status 200 and text appears
self.assertNotContains(response, "Secret draft")
self.assertTemplateUsed(response, "blog/post_list.html")
self.assertRedirects(response, "/login/?next=/blog/new/")
self.assertEqual(response.context["posts"].count(), 3)
self.assertFormError(response.context["form"], "title", "This field is required.")
```

### Logging in during tests

```python
from django.contrib.auth import get_user_model

User = get_user_model()


class ProtectedViewTests(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username="ada", password="pass12345")

    def test_login_required(self):
        response = self.client.get(reverse("blog:post_create"))
        self.assertEqual(response.status_code, 302)           # redirected to login

    def test_logged_in_user_can_access(self):
        self.client.login(username="ada", password="pass12345")
        response = self.client.get(reverse("blog:post_create"))
        self.assertEqual(response.status_code, 200)

    def test_force_login(self):
        self.client.force_login(self.user)                    # skips the password check
        response = self.client.get(reverse("blog:post_create"))
        self.assertEqual(response.status_code, 200)
```

`force_login()` is faster and does not depend on password settings.

### Following redirects

```python
response = self.client.post(reverse("blog:post_create"), data, follow=True)
self.assertRedirects(response, reverse("blog:post_list"))
self.assertContains(response, "Post created")
```

### Testing with uploaded files

```python
from django.core.files.uploadedfile import SimpleUploadedFile

upload = SimpleUploadedFile("note.txt", b"file content", content_type="text/plain")
response = self.client.post(reverse("blog:upload"), {"title": "Doc", "file": upload})
```

### Testing API endpoints

DRF provides `APITestCase` and `APIClient`:

```python
from rest_framework import status
from rest_framework.test import APITestCase


class PostAPITests(APITestCase):
    def test_list_posts(self):
        response = self.client.get("/api/posts/")
        self.assertEqual(response.status_code, status.HTTP_200_OK)

    def test_create_requires_auth(self):
        response = self.client.post("/api/posts/", {"title": "x"}, format="json")
        self.assertEqual(response.status_code, status.HTTP_403_FORBIDDEN)
```

---

## 16.2 Unit and integration tests

### Kinds of tests

| Kind | What it checks | Example |
|---|---|---|
| **Unit test** | One small piece in isolation | A model method returns the right value |
| **Integration test** | Several pieces working together | A view, form, database, and template together |
| **End-to-end test** | The whole system in a real browser | Selenium or Playwright clicking through the site |

A healthy test suite has many fast unit tests, fewer integration tests, and a small number of end-to-end tests.

### TestCase classes

| Class | Use for |
|---|---|
| `django.test.TestCase` | Most tests; each test runs inside a transaction that is rolled back, so it is fast |
| `django.test.TransactionTestCase` | Tests that need real commits (testing `transaction.on_commit`, for example); slower |
| `django.test.SimpleTestCase` | Tests that never touch the database; the fastest |
| `django.test.LiveServerTestCase` | Starts a real server for browser-based tests |
| `rest_framework.test.APITestCase` | DRF API tests |

### Setting up data: setUp and setUpTestData

```python
from django.test import TestCase
from blog.models import Author, Post


class PostModelTests(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Runs ONCE for the whole class; data is shared by all tests.
        cls.author = Author.objects.create(name="Ada")

    def setUp(self):
        # Runs before EACH test method.
        self.post = Post.objects.create(title="Hello", slug="hello", body="Text", author=self.author)

    def tearDown(self):
        # Runs after EACH test (rarely needed with TestCase).
        pass
```

Use `setUpTestData` for data that tests only read, and `setUp` for objects tests modify.

### Unit testing a model

```python
class PostModelTests(TestCase):
    def test_str_returns_title(self):
        post = Post(title="My title")
        self.assertEqual(str(post), "My title")

    def test_slug_must_be_unique(self):
        Post.objects.create(title="A", slug="same", body="x", author=self.author)
        from django.db import IntegrityError
        with self.assertRaises(IntegrityError):
            Post.objects.create(title="B", slug="same", body="y", author=self.author)

    def test_get_absolute_url(self):
        self.assertEqual(self.post.get_absolute_url(), f"/blog/{self.post.pk}/")
```

### Unit testing a form

```python
from blog.forms import PostForm


class PostFormTests(TestCase):
    def test_valid_data(self):
        form = PostForm(data={"title": "Hello there", "slug": "hello-there", "body": "Body"})
        self.assertTrue(form.is_valid())

    def test_title_with_spam_is_rejected(self):
        form = PostForm(data={"title": "Buy spam now", "slug": "x", "body": "Body"})
        self.assertFalse(form.is_valid())
        self.assertIn("title", form.errors)
```

### Integration testing a view

```python
class PostCreateViewTests(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username="ada", password="pass12345")
        self.client.force_login(self.user)

    def test_create_post(self):
        data = {"title": "New", "slug": "new", "body": "Content", "status": "draft"}
        response = self.client.post(reverse("blog:post_create"), data)

        self.assertEqual(Post.objects.count(), 1)
        post = Post.objects.get()
        self.assertEqual(post.title, "New")
        self.assertRedirects(response, post.get_absolute_url())

    def test_invalid_data_shows_errors(self):
        response = self.client.post(reverse("blog:post_create"), {"title": ""})
        self.assertEqual(response.status_code, 200)
        self.assertFormError(response.context["form"], "title", "This field is required.")
        self.assertEqual(Post.objects.count(), 0)
```

### Common assertions

| Assertion | Checks |
|---|---|
| `assertEqual(a, b)` / `assertNotEqual(a, b)` | Equality |
| `assertTrue(x)` / `assertFalse(x)` | Truthiness |
| `assertIn(a, b)` / `assertNotIn(a, b)` | Membership |
| `assertIsNone(x)` / `assertIsNotNone(x)` | `None` checks |
| `assertRaises(Error)` | An exception is raised |
| `assertQuerySetEqual(qs, values)` | QuerySet contents |
| `assertNumQueries(n)` | Exactly `n` database queries ran |
| `assertLessEqual(a, b)` and friends | Comparisons |

Guarding against N+1 queries (Lesson 7):

```python
def test_list_uses_two_queries(self):
    with self.assertNumQueries(2):
        self.client.get(reverse("blog:post_list"))
```

### Overriding settings in tests

```python
from django.test import override_settings

@override_settings(PAGINATE_BY=5)
def test_pagination(self):
    ...
```

### Mocking external services

Tests should be **fast and repeatable**, so never call real payment gateways, email services, or third-party APIs. Replace them with mocks:

```python
from unittest.mock import patch


class NotificationTests(TestCase):
    @patch("blog.services.requests.post")
    def test_webhook_sent(self, mock_post):
        mock_post.return_value.status_code = 200
        send_webhook({"event": "published"})
        mock_post.assert_called_once()
```

Django also replaces the real email backend during tests, so emails are collected in `django.core.mail.outbox`:

```python
from django.core import mail

def test_contact_sends_email(self):
    self.client.post(reverse("blog:contact"), {"name": "Ada", "email": "a@example.com", "message": "Hi"})
    self.assertEqual(len(mail.outbox), 1)
    self.assertEqual(mail.outbox[0].subject, "New contact message")
```

### Writing good tests

- Test **behavior**, not implementation details.
- Give tests descriptive names (`test_anonymous_user_is_redirected_to_login`).
- Keep each test focused on **one** idea.
- Make tests independent: no test should rely on another having run first.
- Test the unhappy paths too: invalid input, missing objects, permission failures.
- When you fix a bug, first write a test that fails because of it, then fix it.

---

## 16.3 Fixtures and factories

Tests need data. There are three ways to supply it: create objects inline (as above), load **fixtures**, or generate them with **factories**.

### Fixtures

A **fixture** is a file containing serialized database rows (JSON, YAML, or XML) that Django can load.

Create one from existing data:

```bash
python manage.py dumpdata blog --indent 2 > blog/fixtures/sample_posts.json
python manage.py dumpdata auth.User --indent 2 > users.json
```

Example `blog/fixtures/sample_posts.json`:

```json
[
  {
    "model": "blog.author",
    "pk": 1,
    "fields": {"name": "Ada"}
  },
  {
    "model": "blog.post",
    "pk": 1,
    "fields": {
      "title": "Hello",
      "slug": "hello",
      "body": "First post",
      "author": 1,
      "status": "published"
    }
  }
]
```

Load it manually or in tests:

```bash
python manage.py loaddata sample_posts
```

```python
class PostListTests(TestCase):
    fixtures = ["sample_posts"]

    def test_fixture_loaded(self):
        self.assertEqual(Post.objects.count(), 1)
```

Django searches each app's `fixtures/` folder and any folder in the `FIXTURE_DIRS` setting.

**Drawbacks of fixtures:**

- They go **stale** when models change; every new required field means editing every fixture.
- The data is far from the test that uses it, so tests are harder to read.
- They are verbose and easy to get wrong when written by hand.

Fixtures remain useful for **reference data** that the app genuinely needs (such as country lists) and for seeding a development database.

### Factories with factory_boy

A **factory** is code that builds model instances with sensible default values, so each test specifies only the values that matter to it. The popular package is **factory_boy**:

```bash
pip install factory_boy
```

```python
# blog/tests/factories.py
import factory
from django.contrib.auth import get_user_model
from blog.models import Author, Post


class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = get_user_model()
        skip_postgeneration_save = True

    username = factory.Sequence(lambda n: f"user{n}")
    email = factory.LazyAttribute(lambda o: f"{o.username}@example.com")
    password = factory.PostGenerationMethodCall("set_password", "pass12345")


class AuthorFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Author

    name = factory.Faker("name")


class PostFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Post

    title = factory.Faker("sentence", nb_words=4)
    slug = factory.Sequence(lambda n: f"post-{n}")
    body = factory.Faker("paragraph")
    status = "draft"
    author = factory.SubFactory(AuthorFactory)       # creates a related Author automatically
```

Using factories:

```python
post = PostFactory()                                  # saved to the database
post = PostFactory(title="Custom title")              # override one value
posts = PostFactory.create_batch(10, status="published")
unsaved = PostFactory.build()                         # not saved
```

A test using factories reads clearly:

```python
class PublishedListTests(TestCase):
    def test_only_published_posts_are_listed(self):
        PostFactory.create_batch(3, status="published")
        PostFactory.create_batch(2, status="draft")

        response = self.client.get(reverse("blog:post_list"))

        self.assertEqual(len(response.context["posts"]), 3)
```

Useful factory_boy tools:

| Tool | Purpose |
|---|---|
| `factory.Sequence` | Unique, incrementing values (needed for `unique=True` fields) |
| `factory.Faker` | Realistic fake data (names, text, emails, dates) |
| `factory.SubFactory` | Creates a related object for a `ForeignKey` |
| `factory.LazyAttribute` | Computes a value from other fields |
| `factory.post_generation` | Handles many-to-many relations and other post-save work |

### Many-to-many with factories

```python
class PostFactory(factory.django.DjangoModelFactory):
    ...

    @factory.post_generation
    def tags(self, create, extracted, **kwargs):
        if not create or not extracted:
            return
        self.tags.add(*extracted)

# PostFactory(tags=[tag1, tag2])
```

### pytest (optional alternative)

Many teams use **pytest** with **pytest-django** instead of `manage.py test`. It offers simpler syntax (plain `assert`), fixtures, and parametrization:

```bash
pip install pytest pytest-django
```

```python
# pytest.ini
[pytest]
DJANGO_SETTINGS_MODULE = mysite.settings
python_files = tests.py test_*.py
```

```python
import pytest
from django.urls import reverse


@pytest.mark.django_db
def test_post_list_page_loads(client):
    response = client.get(reverse("blog:post_list"))
    assert response.status_code == 200
```

Both approaches work; this lesson uses Django's built-in runner because it needs no extra packages.

### Choosing a data strategy

| Approach | Best for |
|---|---|
| Inline `Model.objects.create()` | Small, simple tests |
| Factories | Most real-world suites |
| Fixtures | Static reference data and development seeding |

---

## 16.4 Coverage

### What is coverage?

**Code coverage** measures which lines of your code ran while your tests executed. It tells you what is **not tested**. The standard tool is **coverage.py**.

```bash
pip install coverage
```

### Running tests with coverage

```bash
coverage run manage.py test
coverage report
```

Example output:

```
Name                       Stmts   Miss  Cover
----------------------------------------------
blog/models.py                24      2    92%
blog/views.py                 48     12    75%
blog/forms.py                 15      0   100%
----------------------------------------------
TOTAL                        140     14    90%
```

| Column | Meaning |
|---|---|
| `Stmts` | Number of executable statements |
| `Miss` | Statements never executed by a test |
| `Cover` | Percentage executed |

### Seeing exactly which lines are missed

```bash
coverage report -m          # adds a "Missing" column listing line numbers
coverage html               # writes an interactive report to htmlcov/index.html
```

Open `htmlcov/index.html` in a browser: untested lines are highlighted in red.

### Configuring coverage

Create a `.coveragerc` file (or a `[tool.coverage]` section in `pyproject.toml`) to exclude files that should not count:

```ini
[run]
source = .
branch = True
omit =
    */migrations/*
    */tests/*
    manage.py
    mysite/asgi.py
    mysite/wsgi.py
    .venv/*

[report]
show_missing = True
exclude_lines =
    pragma: no cover
    def __repr__
    raise NotImplementedError
    if __name__ == .__main__.:
```

`branch = True` also checks that both outcomes of every `if` were exercised, which is a stricter measure than line coverage alone.

Mark a line or block that cannot reasonably be tested with a comment:

```python
if settings.DEBUG:  # pragma: no cover
    ...
```

### Failing the build below a threshold

```bash
coverage report --fail-under=80
```

This command exits with an error if total coverage is under 80%, which makes it useful in continuous integration (Lesson 19).

### Using coverage wisely

- Coverage shows what code **ran**, not whether it was **tested well**. A test with no assertions can give 100% coverage and prove nothing.
- Chasing 100% often leads to low-value tests. A target of **80 to 90%** with meaningful assertions is a sensible goal for many projects.
- Use the report to find **untested important code**, such as permission checks, payment logic, and error handling.
- Treat dropping coverage on a pull request as a signal to look closer, not as a rule to obey blindly.

### A testing routine for every feature

1. Write or update tests for the feature, including failure cases.
2. Run `python manage.py test` and make sure everything passes.
3. Run `coverage run manage.py test` and `coverage report -m` to find gaps.
4. Commit tests together with the code they cover.
5. Let your CI service run the suite automatically on every push.

---

[Previous](./[15]-Django-REST-Framework.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[17]-Security-Best-Practices.md)
