[Previous](./[7]-Configuration-And-Environments.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[9]-Error-Handling-And-Logging.md)

*Building Applications*

# Lesson 8 - Sessions And Cookies

---

## 8.1 How Sessions Work

HTTP is **stateless**: every request is independent, and the server does not remember who you are between requests. Yet websites need to remember things, such as who is logged in or what is in a shopping cart. A **session** solves this.

### The idea

1. The server stores some data about a visitor.
2. The browser holds a small token (usually in a **cookie**) that identifies that data.
3. On every request the browser sends the cookie back, so the server can restore the visitor's data.

### Flask's default: client-side signed sessions

Flask's built-in `session` object stores the data **inside the cookie itself**. To stop users from tampering with it, Flask **signs** the cookie using your `SECRET_KEY`.

```
Server  →  Set-Cookie: session=<signed data>   →  Browser
Browser →  Cookie: session=<signed data>        →  Server (verifies signature, reads data)
```

Key consequences:

| Property | Meaning |
|---|---|
| **Signed** | Users cannot modify the data without invalidating the signature. |
| **Not encrypted** | Users **can read** the contents (it is only base64-encoded). Never store passwords or secrets in it. |
| **Size-limited** | Browsers allow about 4 KB per cookie, so keep session data small. |
| **No server storage** | Nothing to clean up, and it works across multiple servers with the same `SECRET_KEY`. |

### A first example

```python
from flask import Flask, session

app = Flask(__name__)
app.config["SECRET_KEY"] = "dev-only-key"


@app.route("/visit")
def visit():
    session["visits"] = session.get("visits", 0) + 1
    return f"You have visited {session['visits']} times."
```

Refreshing the page increments the counter, because the value travels in the cookie.

### Cookies vs sessions

| Feature | Plain cookie | Flask session |
|---|---|---|
| Stored in | Browser | Browser (signed cookie) by default |
| Tamper-proof | No | Yes (signed) |
| Access in Flask | `request.cookies` / `set_cookie` | `session[...]` |
| Best for | Simple preferences | Login state, flash messages, small per-user data |

---

## 8.2 Storing Session Data

The `session` object behaves like a dictionary.

### Reading, writing, and removing values

```python
from flask import session, redirect, url_for


@app.route("/set-theme/<theme>")
def set_theme(theme):
    session["theme"] = theme                 # write
    return redirect(url_for("show_theme"))


@app.route("/theme")
def show_theme():
    theme = session.get("theme", "light")    # read with a default
    return f"Current theme: {theme}"


@app.route("/reset-theme")
def reset_theme():
    session.pop("theme", None)               # remove one key safely
    return redirect(url_for("show_theme"))


@app.route("/logout")
def logout():
    session.clear()                          # remove everything
    return redirect(url_for("show_theme"))
```

### What you can store

Session values must be serializable to JSON-like types: strings, numbers, booleans, lists, dictionaries, and `None`. Store **IDs and small flags**, not whole objects. For example, keep `session["user_id"] = 42` and look up the user in the database.

### Modifying mutable values

Flask only notices changes when you assign to a key. If you change a list or dictionary **in place**, tell Flask:

```python
cart = session.get("cart", [])
cart.append("book")
session["cart"] = cart              # reassigning registers the change
```

Or explicitly mark it:

```python
session["cart"].append("pen")
session.modified = True
```

### Session lifetime

By default a session cookie disappears when the browser closes. To keep it longer, make it **permanent**:

```python
from datetime import timedelta

app.config["PERMANENT_SESSION_LIFETIME"] = timedelta(days=7)


@app.route("/remember")
def remember():
    session.permanent = True
    session["remembered"] = True
    return "You will stay signed in for up to 7 days."
```

### A shopping cart example

```python
@app.route("/cart/add/<int:product_id>")
def add_to_cart(product_id):
    cart = session.get("cart", [])
    if product_id not in cart:
        cart.append(product_id)
    session["cart"] = cart
    return {"cart": cart}


@app.route("/cart")
def view_cart():
    return {"cart": session.get("cart", [])}
```

### Session cookie settings

| Config key | Purpose | Recommended production value |
|---|---|---|
| `SESSION_COOKIE_NAME` | Name of the cookie | `"session"` (default) |
| `SESSION_COOKIE_HTTPONLY` | Hide the cookie from JavaScript | `True` (default) |
| `SESSION_COOKIE_SECURE` | Send only over HTTPS | `True` |
| `SESSION_COOKIE_SAMESITE` | Cross-site sending policy | `"Lax"` |
| `PERMANENT_SESSION_LIFETIME` | Lifetime of permanent sessions | Choose per app |

---

## 8.3 Secret Keys

Flask uses `SECRET_KEY` to cryptographically **sign** the session cookie. Anyone who knows the key can forge a session and impersonate any user, so protecting it is critical.

### Without a key

```python
app = Flask(__name__)

@app.route("/")
def index():
    session["x"] = 1   # RuntimeError: The session is unavailable because no secret key was set.
    return "Hi"
```

### Generating a strong key

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

This prints a 64-character random hex string. Use a different key for each environment.

### Storing it safely

```python
import os

app.config["SECRET_KEY"] = os.environ["SECRET_KEY"]   # fail loudly if missing
```

- Keep it in an environment variable, a `.env` file (ignored by git), or a secrets manager.
- **Never** hard-code a real key or commit it to a repository.
- Never share it in screenshots, logs, or error reports.

### What happens if you change the key

All existing sessions become invalid, so every user is logged out. This is expected when you rotate the key.

### Rotating without logging everyone out

Flask 3.1 added `SECRET_KEY_FALLBACKS`, a list of **old** keys that are still accepted for verifying existing signatures while new cookies are signed with the current key:

```python
app.config["SECRET_KEY"] = os.environ["SECRET_KEY"]
app.config["SECRET_KEY_FALLBACKS"] = [os.environ["OLD_SECRET_KEY"]]
```

After enough time for old sessions to expire, remove the fallback.

### If a key leaks

1. Generate a new key immediately.
2. Deploy it (all users will be signed out).
3. Review logs for suspicious activity.

---

## 8.4 Server-Side Sessions

With default sessions, all data lives in the cookie. Sometimes that is not ideal:

- You need to store **more than ~4 KB**.
- You want to **revoke** a session instantly (for example "log out all devices").
- You prefer that users **cannot see** the stored data.

A **server-side session** keeps the data on the server (in Redis, a database, or the file system) and puts only a random **session ID** in the cookie.

### Flask-Session

The **Flask-Session** extension replaces Flask's session interface with a server-side one while keeping the same `session[...]` code.

```bash
pip install Flask-Session redis
```

```python
import redis
from flask import Flask, session
from flask_session import Session

app = Flask(__name__)
app.config["SECRET_KEY"] = "dev-only-key"
app.config["SESSION_TYPE"] = "redis"
app.config["SESSION_REDIS"] = redis.from_url("redis://localhost:6379")
app.config["SESSION_PERMANENT"] = False

Session(app)


@app.route("/set")
def set_value():
    session["name"] = "Ada"
    return "Stored on the server"


@app.route("/get")
def get_value():
    return session.get("name", "nothing stored")
```

Your view code does not change at all. Only the storage backend is different.

### Available backends

Flask-Session supports several backends, selected with `SESSION_TYPE`. The exact list and option names depend on the Flask-Session version, so check its documentation when you install it. Common choices include:

| Backend | Good for |
|---|---|
| Redis | Production: fast, supports expiry, shared across servers |
| Memcached | Fast cache-based storage |
| SQLAlchemy | Storing sessions in your existing relational database |
| MongoDB | Apps already using MongoDB |
| Cache-based / file-based | Local development or small single-server setups |

### Client-side vs server-side

| Aspect | Client-side (default) | Server-side |
|---|---|---|
| Setup | None | Needs an extension and a backend |
| Data size | About 4 KB | Large |
| Data visible to user | Readable (not editable) | Hidden |
| Instant revocation | Hard | Easy (delete the server record) |
| Extra infrastructure | None | Redis, database, etc. |
| Scales across servers | Yes, with the same key | Yes, with a shared backend |

### Which should you choose?

- Start with Flask's **default sessions** for login state and small flags. They are simple and fast.
- Move to **server-side sessions** when you need larger data, immediate revocation, or hidden data.

### Security reminders

- Always use HTTPS in production and set `SESSION_COOKIE_SECURE = True`.
- Keep `SESSION_COOKIE_HTTPONLY = True` so JavaScript cannot read the session cookie.
- Use `SESSION_COOKIE_SAMESITE = "Lax"` to help protect against CSRF.
- Regenerate or clear the session on login and logout to avoid session fixation.

---

[Previous](./[7]-Configuration-And-Environments.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[9]-Error-Handling-And-Logging.md)
