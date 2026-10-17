[Previous](./[6]-Architecture-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[8]-Components-And-Rendering.md)

*Application Structure*

# Lesson 7 - Development Workflow

Knowing what a framework is only gets you so far. Day to day, what matters is the *workflow*: how you start a project, run it while you edit, find out why something broke, and keep settings separate from code. This lesson covers the tools and habits that nearly every framework shares.

## 7.1 Dev Servers and Hot Reloading

A **development server** (dev server) is a local web server that runs your application on your own computer while you work on it, typically at an address such as `http://localhost:3000`. It is built for convenience, not for real users.

Dev servers usually give you:

- **Fast startup**, so you can see your changes quickly.
- **Helpful error messages**, often shown right in the browser, with details about what went wrong.
- **Automatic reloading** when you save a file.

### Types of automatic reloading

| Technique | What happens when you save a file | Examples |
|---|---|---|
| **Auto-restart** | The server process restarts and loads your new code | Flask debug mode, Django's `runserver`, `uvicorn --reload` |
| **Live reload** | The browser page refreshes fully | Many simple static servers |
| **Hot module replacement (HMR)** | Only the changed module is swapped in, and the page keeps its current state | Vite, webpack, Next.js, Angular, Vue.js, Svelte |
| **Stateful hot reload** | The changed code is injected into the running app, which keeps its state | Flutter |

Hot reloading shortens the loop between *change a line* and *see the result* from many seconds to nearly instant, which makes experimenting far more enjoyable.

### Starting a dev server

The exact command differs between frameworks, but the idea is always the same:

| Framework | Typical command to start the dev server |
|---|---|
| React, Vue.js, Svelte (with Vite) | `npm run dev` |
| Next.js | `npm run dev` |
| Angular | `ng serve` |
| Django | `python manage.py runserver` |
| Flask | `flask --app app run --debug` |
| FastAPI | `uvicorn main:app --reload` |
| Laravel | `php artisan serve` |
| ASP.NET Core | `dotnet watch run` |
| Flutter | `flutter run` |

Always check your framework's documentation for the current command, because tools evolve.

### Dev server vs production server

> **Never use the dev server to serve real users.** It is deliberately not optimized for speed, security, or handling many visitors. Production uses a dedicated setup instead (Lesson 17). Frameworks usually print a warning about this when you start the dev server.

---

## 7.2 CLIs and Scaffolding Tools

A **CLI (command-line interface)** is a program you run by typing commands in a terminal. Almost every framework ships with one, and it does two big jobs.

### Scaffolding a new project

**Scaffolding** means generating a ready-to-run starting project: folders, config files, and a sample page. This saves you from assembling the structure from memory.

| Framework | Command to create a new project |
|---|---|
| React / Vue.js (with Vite) | `npm create vite@latest` |
| Next.js | `npx create-next-app@latest` |
| Angular | `ng new my-app` |
| NestJS | `nest new my-app` |
| Django | `django-admin startproject mysite` |
| Laravel | `laravel new my-app` |
| Ruby on Rails | `rails new my-app` |
| ASP.NET Core | `dotnet new webapp` |
| Flutter | `flutter create my_app` |
| Spring Boot | The Spring Initializr website (start.spring.io), which generates a project to download |

These tools often ask a few questions (use TypeScript? include testing?) and then create the structure for you.

### Generating pieces of an existing project

Once a project exists, the same CLI usually generates new files that follow the project's conventions:

| Framework | Example generator command |
|---|---|
| Angular | `ng generate component task-list` |
| Laravel | `php artisan make:controller TaskController` |
| Django | `python manage.py startapp tasks` |
| Rails | `rails generate model Task title:string` |
| NestJS | `nest generate module tasks` |

Generators ensure the file lands in the right folder with the right name and boilerplate, which is the "convention" part of Lesson 4 at work.

### Project scripts

Many projects define short named commands in their manifest (for example, the `scripts` section of `package.json`), so everyone runs the same thing:

```bash
npm run dev     # start the dev server
npm run build   # create a production build
npm test        # run the tests
```

When you join an existing project, look at these scripts first. They tell you how the team expects the project to be run.

---

## 7.3 Debugging Basics

**Debugging** is the process of finding out why your program does not do what you expect. It is a normal, daily part of development, not a sign that something is wrong with you.

### Step 1: Read the error message

Beginners often look away from error messages, but they usually tell you exactly where to look. Consider this small program, which tries to compute a total for a list of items where one item is missing its `discount` field:

```python
def get_discount(price, percent):
    return price - price * percent / 100


def checkout(items):
    total = 0
    for item in items:
        total += get_discount(item["price"], item["discount"])
    return total


print(checkout([{"price": 100, "discount": 10}, {"price": 50}]))
```

Running it produces this:

```text
Traceback (most recent call last):
  File "traceback_demo.py", line 12, in <module>
    print(checkout([{"price": 100, "discount": 10}, {"price": 50}]))
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "traceback_demo.py", line 8, in checkout
    total += get_discount(item["price"], item["discount"])
                                         ~~~~^^^^^^^^^^^^
KeyError: 'discount'
```

Reading from the bottom up:

1. **The last line is the error itself**: `KeyError: 'discount'` means "you asked for the key `discount`, and it does not exist."
2. **The lines above are the stack trace**: the chain of function calls that led there. The line `total += get_discount(item["price"], item["discount"])` in `checkout` is where the problem happened.
3. **The first line of the trace** shows where the whole thing started.

The fix is now clear: the second item has no `discount`, so the code must handle missing data.

### Step 2: Reproduce and narrow it down

- Find the **smallest set of steps** that makes the problem happen every time.
- **Isolate** the cause: comment out parts, test one function alone, or check a single input.
- Ask "what did I change last?" Recent changes are the most likely culprit, and version control lets you compare.

### Step 3: Use your tools

| Tool | What it does |
|---|---|
| **Logging / print statements** | Shows the value of variables and which code actually ran |
| **Debugger with breakpoints** | Pauses the program on a chosen line so you can inspect variables and step through line by line (built into editors such as VS Code and IDEs such as IntelliJ and Android Studio) |
| **Browser developer tools** | Press F12 in a browser. *Console* shows JavaScript errors, *Elements* inspects the page, *Network* shows every request and response, *Sources* lets you set breakpoints |
| **Framework developer tools** | Browser extensions or built-in panels that inspect components and state in React, Vue.js, and Angular, and the DevTools suite in Flutter |
| **Network inspection** | Confirms whether the frontend sent the request you thought it did and what the server replied |

### Common categories of problems

| Symptom | Likely cause |
|---|---|
| The page is blank and the browser console shows a red error | A JavaScript error in your code |
| A request fails with 404 | Wrong URL, or the route is not registered |
| A request fails with 500 | An error on the server; check the server's logs and stack trace |
| It works on your machine but not elsewhere | Different versions, missing environment variables, or different data |
| An old behavior keeps appearing | Browser cache, or you are not running the code you think you are |

### Good debugging habits

- **Change one thing at a time**, then re-test.
- **Do not guess blindly**: form a theory, test it, and keep what you learn.
- **Search the exact error message**, and read the framework's documentation.
- **Explain the problem out loud** (even to a rubber duck). Putting it into words often reveals the answer.
- **Fix the cause, not the symptom.**

---

## 7.4 Environment Variables and Configuration

Some settings should not be written directly into your code:

- **Secrets**, such as database passwords and API keys, which must never be exposed.
- **Values that differ by environment**, such as the database address on your laptop versus on the production server.

The standard solution is **environment variables**: named values that live *outside* the code and are read by the program when it starts.

### Reading environment variables

Here is a small program that reads two settings, with sensible defaults for when they are missing:

```python
import os

database_url = os.environ.get("DATABASE_URL", "sqlite:///dev.db")
debug = os.environ.get("DEBUG", "false").lower() == "true"

print(f"database={database_url} debug={debug}")
```

With no variables set, it uses the defaults:

```text
database=sqlite:///dev.db debug=False
```

The values can be supplied from outside without changing the code:

```bash
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb DEBUG=true python env_demo.py
```

```text
database=postgresql://user:pass@localhost:5432/mydb debug=True
```

The same code ran with different settings, which is exactly the point. Every language has an equivalent: `process.env.NAME` in Node.js, `os.environ` in Python, `getenv()` in PHP, and `System.getenv()` in Java.

### `.env` files

Typing variables on the command line is tedious, so frameworks and tools commonly load them from a `.env` file at startup:

```text
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
SECRET_KEY=a-long-random-value
DEBUG=true
```

The rules for `.env` files are important:

1. **Add `.env` to `.gitignore`** so it is never committed.
2. **Commit a `.env.example`** with the variable *names* and harmless placeholder values, so teammates know what to set.
3. **Never put real secrets in the example file.**
4. If a secret is ever committed by accident, treat it as exposed and **rotate it** (generate a new one), since deleting it from the repository history does not undo the exposure.

### Per-environment configuration

Applications typically run in several environments (development, testing, production), each with different settings. Frameworks help with this in different ways: separate settings files, per-environment `.env` files, or configuration chosen by a variable such as `APP_ENV` or `NODE_ENV`. In production, secrets are often supplied by the hosting platform or a dedicated **secrets manager** rather than a file on disk.

### A warning about frontend configuration

Code that runs in the browser is **visible to everyone** who visits the site. Any environment variable that gets bundled into frontend code is therefore public. Frontend tools make this explicit by requiring a special prefix for variables that are allowed to reach the browser (for example, `VITE_` in Vite or `NEXT_PUBLIC_` in Next.js). Use those only for non-secret values, such as the public address of your API. **Real secrets belong on the server only.**

---

[Previous](./[6]-Architecture-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[8]-Components-And-Rendering.md)
