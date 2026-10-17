[Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[2]-Projects-And-Apps.md)

*Basics*

# Lesson 1 - What Is Django

---

## 1.1 The batteries-included philosophy

Django is a high-level Python web framework. "High-level" means it handles the repetitive parts of web development for you so you can focus on what makes your site unique. Its motto is often summarized as *"the web framework for perfectionists with deadlines."*

Django is called **"batteries-included"** because it ships with the tools most websites need, instead of asking you to find and wire together many separate libraries.

| Built-in feature | What it does |
|---|---|
| **ORM** | Lets you work with your database using Python classes instead of raw SQL |
| **Admin interface** | Auto-generates a full management site for your data |
| **Authentication** | Users, passwords, login/logout, permissions, and groups |
| **Forms** | Renders HTML forms and validates submitted data |
| **Templating** | A safe, simple language for generating HTML |
| **URL routing** | Maps URLs to the Python code that handles them |
| **Security** | Protections against CSRF, XSS, SQL injection, and clickjacking |
| **Sessions & caching** | Remember users between requests and speed up responses |
| **Migrations** | Version control for your database schema |

The trade-off is that Django is **opinionated**: it has a preferred way to structure projects. For beginners this is a benefit, because there is a clear "Django way" to do most things. Other frameworks (such as Flask) are "micro-frameworks" that give you very little by default and let you choose every component yourself.

Django follows several guiding principles:

- **DRY (Don't Repeat Yourself)** — define something once and reuse it everywhere.
- **Loose coupling** — the layers of the framework (database, templates, URLs) know as little about each other as possible.
- **Explicit is better than implicit** — Django favors clear, readable code over hidden magic.

---

## 1.2 Django's MTV architecture

Many frameworks use the **MVC** (Model-View-Controller) pattern. Django uses a variation called **MTV**: **Model, Template, View**.

| Django layer | Responsibility | MVC equivalent |
|---|---|---|
| **Model** | Describes your data and talks to the database | Model |
| **Template** | Defines how data is presented (HTML) | View |
| **View** | Contains the logic that handles a request and returns a response | Controller |

The "controller" part of MVC is handled by the **Django framework itself**, mainly through its URL routing system, which decides which view should handle each request.

### The request/response cycle

Here is what happens when a user visits a page on your Django site:

1. The browser sends an **HTTP request** (for example `GET /posts/`).
2. Django passes the request through **middleware** (small processing layers for security, sessions, and so on).
3. The **URL resolver** compares the path against your URL patterns and picks a **view**.
4. The **view** runs your logic. It may ask a **model** to fetch data from the database.
5. The view passes that data to a **template**, which produces HTML.
6. The view returns an **HTTP response**, which travels back through middleware to the browser.

```
Browser -> Middleware -> URL router -> View -> (Model <-> Database)
Browser <- Middleware <- Response <- Template <-+
```

Understanding this flow is the key to understanding every other lesson in this Topic.

---

## 1.3 Virtual environments and installation

### Prerequisites

Django is a Python package, so you need Python 3 installed. Check your version:

```bash
python --version
```

Each Django release supports a specific range of Python versions, so check the release notes at [djangoproject.com](https://www.djangoproject.com/) if you are unsure.

### Why use a virtual environment?

A **virtual environment** is an isolated folder containing its own Python packages. Without one, every project on your computer shares the same global packages, and different projects needing different versions of Django will conflict. Always create one virtual environment per project.

### Creating and activating a virtual environment

```bash
# Create the environment in a folder named .venv
python -m venv .venv

# Activate it (macOS / Linux)
source .venv/bin/activate

# Activate it (Windows, Command Prompt)
.venv\Scripts\activate

# Activate it (Windows, PowerShell)
.venv\Scripts\Activate.ps1
```

When active, your terminal prompt usually shows `(.venv)`. To leave the environment, run `deactivate`.

### Installing Django

```bash
pip install django
```

Verify the installation:

```bash
python -m django --version
```

### Recording dependencies

Save your project's packages so others (and your future self) can recreate the environment:

```bash
pip freeze > requirements.txt

# Later, on another machine:
pip install -r requirements.txt
```

Add `.venv/` to your `.gitignore` so the environment itself is never committed.

---

## 1.4 Creating your first project

Django includes a command-line tool called `django-admin` for creating projects. Create a project named `mysite`:

```bash
django-admin startproject mysite
cd mysite
```

Django generates this structure:

```
mysite/
├── manage.py
└── mysite/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── asgi.py
    └── wsgi.py
```

| File | Purpose |
|---|---|
| `manage.py` | Command-line utility for running the server, migrations, tests, and more |
| `mysite/__init__.py` | Empty file that marks the folder as a Python package |
| `mysite/settings.py` | All project configuration (database, installed apps, middleware, etc.) |
| `mysite/urls.py` | The root URL configuration |
| `mysite/asgi.py` | Entry point for ASGI-compatible servers (async) |
| `mysite/wsgi.py` | Entry point for WSGI-compatible servers (traditional) |

To avoid a redundant nested folder, you can add a trailing dot so the project is created in the current directory:

```bash
django-admin startproject mysite .
```

### Running the project

Start the development server:

```bash
python manage.py runserver
```

Open `http://127.0.0.1:8000/` in your browser. You should see Django's welcome page with a rocket graphic. You may also see a message about "unapplied migrations"; that is normal and is covered in Lesson 6.

Congratulations, you now have a working Django project. In the next lesson you will learn how a project is divided into **apps**.

---

[Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[2]-Projects-And-Apps.md)
