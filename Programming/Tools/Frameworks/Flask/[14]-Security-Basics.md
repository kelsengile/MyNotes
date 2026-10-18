[Previous](./[13]-Authentication-With-Flask-Login.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[15]-Testing-With-Pytest.md)

*Security*

# Lesson 14 - Security Basics

---

## 14.1 Common Vulnerabilities

Security is not a feature you add at the end; it is a habit applied throughout development. This sub-lesson covers the most common web vulnerabilities and how Flask helps you avoid them. The categories below overlap with the well-known **OWASP Top 10** list of web risks.

### Cross-Site Scripting (XSS)

**What it is:** an attacker gets their JavaScript to run in other users' browsers, usually by submitting text containing `<script>` tags that your site later displays.

**Defense in Flask:** Jinja2 **auto-escapes** variables in HTML templates.

```html
<!-- If comment = "<script>alert(1)</script>", the browser shows it as harmless text -->
<p>{{ comment }}</p>
```

Things that bypass this protection:

```html
{{ comment | safe }}              <!-- disables escaping: dangerous with user input -->
{% autoescape false %}...{% endautoescape %}
```

```python
from markupsafe import Markup
Markup(user_input)                # marks text as safe: dangerous with user input
```

Rules:

- Never use `|safe` or `Markup` on user-supplied content unless you **sanitize** it first with a library such as `bleach` or `nh3`.
- Always put attribute values in **quotes**: `<input value="{{ value }}">`.
- Use `{{ data | tojson }}` when passing values into JavaScript, never string-formatting them in.

### SQL Injection

**What it is:** user input is pasted into a SQL string and changes the query's meaning.

```python
# DANGEROUS: never build SQL with string formatting
query = f"SELECT * FROM user WHERE username = '{username}'"
# Input: ' OR '1'='1   -> returns every user
```

**Defense:** use the ORM or **parameterized queries**:

```python
user = db.session.scalar(db.select(User).where(User.username == username))
```

When you need raw SQL, pass parameters separately:

```python
from sqlalchemy import text

db.session.execute(
    text("SELECT * FROM user WHERE username = :name"),
    {"name": username},
)
```

### Cross-Site Request Forgery (CSRF)

**What it is:** another site tricks a logged-in user's browser into sending a request to your app. **Defense:** CSRF tokens on every state-changing form (see Lesson 5) plus `SameSite` cookies (see 14.2).

### Broken Authentication and Access Control

- Weak or unhashed passwords. **Fix:** hash with Werkzeug or Argon2 (Lesson 13).
- Missing ownership checks (IDOR). **Fix:** verify `post.user_id == current_user.id` on every access.
- Admin pages that rely on hidden URLs. **Fix:** enforce roles on the server.

### Open Redirects

Redirecting to a URL taken from user input can send people to a phishing site. Validate that `next` targets are relative paths on your site (see Lesson 13).

### Clickjacking

Another site embeds yours in a hidden frame and tricks users into clicking. **Defense:** send headers that forbid framing (see 14.2).

### Sensitive Data Exposure

- Leaving `DEBUG=True` in production.
- Returning full error tracebacks to users.
- Logging passwords or tokens.
- Sending data over plain HTTP instead of HTTPS.

### Other risks to keep in mind

| Risk | Short defense |
|---|---|
| Insecure file uploads | Validate type and size, use `secure_filename`, store outside the web root |
| Brute-force logins | Rate limit and lock out repeated failures |
| Vulnerable dependencies | Keep packages updated, scan with `pip-audit` |
| Server-side request forgery | Never fetch arbitrary user-supplied URLs without restrictions |
| Missing security headers | Add them globally (see 14.2) |

---

## 14.2 Secure Cookies

Cookies carry session data, so their flags matter a great deal.

### The key flags

| Flag | Effect | Protects against |
|---|---|---|
| `HttpOnly` | JavaScript cannot read the cookie. | Session theft through XSS |
| `Secure` | Browser sends the cookie only over HTTPS. | Interception on insecure networks |
| `SameSite` | Limits when cookies accompany cross-site requests. | CSRF |

### Flask configuration

```python
app.config.update(
    SESSION_COOKIE_HTTPONLY=True,       # default is already True
    SESSION_COOKIE_SECURE=True,         # require HTTPS (set False only for local HTTP dev)
    SESSION_COOKIE_SAMESITE="Lax",      # "Lax" is a good default; "Strict" is tighter
    REMEMBER_COOKIE_HTTPONLY=True,      # Flask-Login's remember-me cookie
    REMEMBER_COOKIE_SECURE=True,
    REMEMBER_COOKIE_SAMESITE="Lax",
    PERMANENT_SESSION_LIFETIME=3600 * 24 * 7,  # seconds
)
```

`SameSite` values:

| Value | Behavior |
|---|---|
| `Strict` | Cookie is never sent on cross-site requests, even when following a link from another site. |
| `Lax` | Cookie is sent for top-level navigations (clicking a link) but not for embedded or cross-site POST requests. |
| `None` | Always sent. Requires `Secure`. Use only when you truly need cross-site behavior. |

### Setting secure cookies manually

```python
response.set_cookie(
    "preference",
    "dark",
    max_age=3600,
    httponly=True,
    secure=True,
    samesite="Lax",
)
```

### HTTPS everywhere

- Terminate TLS at Nginx or your hosting platform (Lesson 17).
- Redirect HTTP to HTTPS.
- Send the **HSTS** header so browsers always use HTTPS for your domain.

### Security headers

Add protective headers to every response:

```python
@app.after_request
def set_security_headers(response):
    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    response.headers["Referrer-Policy"] = "strict-origin-when-cross-origin"
    response.headers["Content-Security-Policy"] = "default-src 'self'"
    if not app.debug:
        response.headers["Strict-Transport-Security"] = "max-age=31536000; includeSubDomains"
    return response
```

| Header | Purpose |
|---|---|
| `X-Content-Type-Options: nosniff` | Stops browsers guessing file types |
| `X-Frame-Options: DENY` | Prevents clickjacking (CSP `frame-ancestors` is the modern equivalent) |
| `Content-Security-Policy` | Restricts where scripts, styles, and images may load from, reducing XSS impact |
| `Strict-Transport-Security` | Forces HTTPS |
| `Referrer-Policy` | Limits what URL information is shared with other sites |

A strict CSP such as `default-src 'self'` blocks inline scripts and external resources, so tune it to your app. The **Flask-Talisman** extension sets these headers with sensible defaults:

```bash
pip install flask-talisman
```

```python
from flask_talisman import Talisman

Talisman(app, content_security_policy={"default-src": "'self'"})
```

### Behind a proxy

When Nginx terminates HTTPS and forwards plain HTTP to Flask, Flask must trust the proxy headers to know the original scheme. Use `ProxyFix` (see Lesson 17):

```python
from werkzeug.middleware.proxy_fix import ProxyFix

app.wsgi_app = ProxyFix(app.wsgi_app, x_for=1, x_proto=1, x_host=1)
```

---

## 14.3 Input Validation

**Rule: never trust input.** Anything arriving from outside your server is untrusted: form fields, query strings, JSON bodies, headers, cookies, uploaded files, and URL parts.

### Validate, then use

Validation checks that data has the expected **type, shape, range, and format** before you use it.

```python
@app.get("/search")
def search():
    page = request.args.get("page", 1, type=int)       # converts or falls back to 1
    if page < 1 or page > 1000:
        abort(400, description="Invalid page number")
    ...
```

Prefer **allow-lists** (accept only known-good values) over block-lists:

```python
ALLOWED_SORTS = {"date", "title", "author"}
sort = request.args.get("sort", "date")
if sort not in ALLOWED_SORTS:
    abort(400)
```

### Use libraries for structured input

| Input type | Tool |
|---|---|
| HTML forms | Flask-WTF / WTForms (Lesson 5) |
| JSON APIs | Marshmallow or Pydantic (Lesson 12) |
| URL parts | Route converters like `<int:id>` |

### Limit sizes

```python
app.config["MAX_CONTENT_LENGTH"] = 5 * 1024 * 1024   # reject bodies over 5 MB with a 413
```

### Safe file uploads

```python
import os
import uuid
from werkzeug.utils import secure_filename

ALLOWED_EXTENSIONS = {"png", "jpg", "jpeg", "gif"}
UPLOAD_DIR = "/var/app/uploads"       # outside the static folder


def allowed_file(filename):
    return "." in filename and filename.rsplit(".", 1)[1].lower() in ALLOWED_EXTENSIONS


@app.post("/upload")
def upload():
    file = request.files.get("image")
    if file is None or file.filename == "":
        abort(400, description="No file provided")
    if not allowed_file(file.filename):
        abort(400, description="File type not allowed")

    ext = file.filename.rsplit(".", 1)[1].lower()
    safe_name = f"{uuid.uuid4().hex}.{ext}"          # random name avoids collisions and tricks
    file.save(os.path.join(UPLOAD_DIR, safe_name))
    return {"filename": safe_name}, 201
```

Additional upload advice:

- File extensions can be faked. For higher assurance, inspect the actual content (for example with an imaging library).
- Never execute uploaded files.
- Serve uploads through a controlled route or a separate storage service.
- When you must use the client's original name, always pass it through `secure_filename`.

### Validate on the server

Browser validation (`required`, `maxlength`, JavaScript checks) improves user experience but can be bypassed. **Server-side validation is mandatory.**

### Output encoding

Validation reduces bad input, but **encoding on output** is what prevents injection. Rely on Jinja2 autoescaping for HTML and parameterized queries for SQL.

### Rate limiting

Protect login, signup, and expensive endpoints from abuse:

```bash
pip install Flask-Limiter
```

```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(get_remote_address, app=app, default_limits=["200 per day", "50 per hour"])


@app.post("/login")
@limiter.limit("5 per minute")
def login():
    ...
```

---

## 14.4 Secrets Handling

A **secret** is any value that grants access or proves identity: `SECRET_KEY`, database passwords, API keys, OAuth client secrets, signing keys, and tokens.

### Rules

1. **Never commit secrets to version control.** Git history is permanent; even a deleted secret can be recovered.
2. **Never hard-code them** in source files.
3. **Use different secrets** for development, testing, and production.
4. **Give each secret the least privilege** it needs (for example a database user without admin rights).
5. **Rotate secrets** regularly and immediately after any suspected leak.

### Where to store secrets

| Option | Suitable for |
|---|---|
| Environment variables | Most apps and containers |
| `.env` file (git-ignored) | Local development |
| Instance folder config (git-ignored) | Single-server deployments |
| Platform secret stores (cloud secret managers, CI/CD secrets) | Production at scale |

### Loading them

```python
import os

SECRET_KEY = os.environ["SECRET_KEY"]                 # required: fails fast if missing
STRIPE_API_KEY = os.environ.get("STRIPE_API_KEY")     # optional
```

### `.gitignore` essentials

```
.env
.env.*
instance/
*.pem
*.key
```

Provide a safe template for teammates, such as `.env.example`, containing **names only** with fake values:

```
SECRET_KEY=replace-me
DATABASE_URL=sqlite:///app.db
```

### If a secret is committed by mistake

1. **Revoke or rotate** it immediately. Assume it is compromised.
2. Remove it from the repository history using tools like `git filter-repo`, but understand that copies may already exist.
3. Add the file to `.gitignore`.
4. Consider adding a secret scanner such as `gitleaks` or GitHub secret scanning to catch future mistakes.

### Keep secrets out of logs and responses

```python
# BAD
app.logger.info("Login attempt: %s / %s", username, password)

# GOOD
app.logger.info("Login attempt for user %s", username)
```

- Do not return configuration objects or environment dumps from any endpoint.
- Disable debug mode in production so tracebacks (which can reveal values) are never shown.

### Dependencies and updates

- Pin dependency versions in `requirements.txt`.
- Update regularly, especially Flask, Werkzeug, Jinja2, and security-relevant extensions.
- Scan for known vulnerabilities:

```bash
pip install pip-audit
pip-audit
```

### Security checklist before launch

- [ ] `DEBUG` is off
- [ ] `SECRET_KEY` is long, random, and loaded from the environment
- [ ] HTTPS is enforced, with secure cookie flags set
- [ ] CSRF protection covers all state-changing requests
- [ ] Passwords are hashed with a strong algorithm
- [ ] Ownership and role checks exist on private data
- [ ] Input is validated; uploads are restricted
- [ ] Security headers are set
- [ ] Rate limiting is applied to sensitive endpoints
- [ ] Dependencies are current, and secrets are not in version control

---

[Previous](./[13]-Authentication-With-Flask-Login.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[15]-Testing-With-Pytest.md)
