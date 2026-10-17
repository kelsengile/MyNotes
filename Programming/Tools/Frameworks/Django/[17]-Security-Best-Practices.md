[Previous](./[16]-Testing-Django-Applications.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[18]-Settings-And-Environments.md)

*Quality And Delivery*

# Lesson 17 - Security Best Practices

---

## 17.1 Built-in protections

Django was designed with security in mind, and many common web attacks are blocked **by default**, provided you use the framework the way it expects. Understanding what is protected, and how you could accidentally switch it off, is the foundation of a secure site.

### Cross-site request forgery (CSRF)

**The attack:** a malicious site tricks a logged-in user's browser into sending a request to your site (for example, submitting a hidden form that changes their email address). The browser attaches the user's cookies automatically, so your site thinks the request is legitimate.

**Django's defense:** `CsrfViewMiddleware` requires a secret token on every unsafe request (`POST`, `PUT`, `PATCH`, `DELETE`). The token is embedded in your forms and checked on submission.

```html
<form method="post">
  {% csrf_token %}
  ...
</form>
```

For JavaScript requests, send the token in a header:

```javascript
fetch("/api/like/", {
  method: "POST",
  headers: {"X-CSRFToken": getCookie("csrftoken")},
});
```

How you could break it:

- Forgetting `{% csrf_token %}` (the request fails with a 403 error, which is the safe outcome).
- Using the `@csrf_exempt` decorator carelessly. Use it only for endpoints that authenticate in another way (such as webhooks with a signature).
- Changing the state of your site with a `GET` request. `GET` requests are not CSRF-protected, so they must never modify data.

### Cross-site scripting (XSS)

**The attack:** an attacker gets their JavaScript to run in other users' browsers, for example by posting a comment containing `<script>` that steals session data.

**Django's defense:** templates **automatically escape** special HTML characters in variables, so `<script>` is shown as text:

```html
{{ comment.text }}      <!-- safe: escaped -->
```

How you could break it:

```html
{{ comment.text|safe }}                <!-- DANGEROUS with user input -->
{% autoescape off %}...{% endautoescape %}
```

```python
from django.utils.safestring import mark_safe
mark_safe(user_input)                   # DANGEROUS
```

Rules:

- Never mark user-supplied content as safe.
- If you must allow some HTML (rich-text comments), **sanitize** it with a library such as `nh3` or `bleach` before storing or displaying it.
- Be careful when placing variables inside JavaScript or inside HTML attributes without quotes. Use `{{ value|json_script:"data-id" }}` to pass data to JavaScript safely.

### SQL injection

**The attack:** user input is pasted into an SQL string, letting the attacker run their own SQL (`'; DROP TABLE users; --`).

**Django's defense:** the ORM **parameterizes** every query, keeping data separate from SQL code:

```python
Post.objects.filter(title=user_input)         # safe
```

How you could break it:

```python
# DANGEROUS: string formatting inside raw SQL
Post.objects.raw(f"SELECT * FROM blog_post WHERE title = '{user_input}'")
cursor.execute("SELECT * FROM blog_post WHERE title = '%s'" % user_input)

# SAFE: pass parameters separately
Post.objects.raw("SELECT * FROM blog_post WHERE title = %s", [user_input])
cursor.execute("SELECT * FROM blog_post WHERE title = %s", [user_input])
```

Treat `.raw()`, `.extra()`, `RawSQL`, and `cursor.execute()` as places that need special care.

### Clickjacking

**The attack:** your site is loaded invisibly inside an `<iframe>` on a malicious page, and the user is tricked into clicking buttons on your site.

**Django's defense:** `XFrameOptionsMiddleware` adds the header `X-Frame-Options: DENY`, which stops browsers from displaying your pages in frames on other sites.

```python
X_FRAME_OPTIONS = "DENY"           # the default for new projects (or "SAMEORIGIN")
```

### Password storage

Passwords are stored as salted, slow hashes (PBKDF2 by default), never in plain text (Lesson 12).

### Host header validation

**The attack:** forged `Host` headers can poison password-reset links or caches.

**Django's defense:** `ALLOWED_HOSTS` lists the host names your site may serve, and requests with any other host are rejected:

```python
ALLOWED_HOSTS = ["example.com", "www.example.com"]
```

### Other built-in protections

| Protection | Details |
|---|---|
| **Session security** | Session IDs are random, and cookies are `HttpOnly` by default |
| **Safe file serving** | `FileResponse` and static handling avoid path traversal when used correctly |
| **Secure random values** | `get_random_secret_key()` and the `secrets` module for tokens |
| **Signed data** | `django.core.signing` creates tamper-proof tokens and cookies |
| **Email header injection** | Django rejects newlines in email headers |

### Summary: your responsibilities

| Threat | Django protects you if you... |
|---|---|
| CSRF | Include `{% csrf_token %}` and never change state with GET |
| XSS | Do not use `\|safe` / `mark_safe` with user input |
| SQL injection | Use the ORM, or pass raw SQL parameters separately |
| Clickjacking | Keep `XFrameOptionsMiddleware` enabled |
| Host spoofing | Set `ALLOWED_HOSTS` correctly |
| Weak access control | Check permissions in every view (Lesson 12) |

No framework can protect you from **broken access control**, such as a view that lets any user edit any object by changing the number in the URL. Always check that the current user is allowed to access the specific object (for example by filtering with `author=request.user`).

---

## 17.2 Settings hardening

Many security problems come from deploying with development settings. This section lists the settings that matter most in production.

### The essentials

```python
# settings.py (production)
DEBUG = False
ALLOWED_HOSTS = ["example.com", "www.example.com"]
```

- **`DEBUG = False`** is non-negotiable. With `DEBUG = True`, error pages reveal source code, settings, and SQL queries to anyone.
- **`ALLOWED_HOSTS`** must list your real domain names. Never use `["*"]` in production.

### Force HTTPS

**HTTPS** encrypts traffic between browser and server. Without it, passwords and session cookies can be stolen on public networks.

```python
SECURE_SSL_REDIRECT = True               # redirect all HTTP requests to HTTPS

SESSION_COOKIE_SECURE = True             # send the session cookie only over HTTPS
CSRF_COOKIE_SECURE = True                # send the CSRF cookie only over HTTPS
```

### HTTP Strict Transport Security (HSTS)

HSTS tells browsers to **always** use HTTPS for your site for a period of time, even if the user types `http://`.

```python
SECURE_HSTS_SECONDS = 31536000           # one year
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
```

Start with a **small** value such as `300` seconds while you test, because a mistake with a long HSTS time can lock users out of your site for that period.

### Running behind a proxy or load balancer

Most production setups terminate HTTPS at a proxy (Nginx, a cloud load balancer) that then talks plain HTTP to Django. Django must be told to trust the forwarded header, otherwise `SECURE_SSL_REDIRECT` causes an infinite redirect loop:

```python
SECURE_PROXY_SSL_HEADER = ("HTTP_X_FORWARDED_PROTO", "https")
```

Only set this if your proxy **always** overwrites that header. Otherwise, clients could spoof it.

### Trusted origins for CSRF

If your site is served over HTTPS and accepts form posts from specific other origins (or sits behind some proxies), list them with the scheme:

```python
CSRF_TRUSTED_ORIGINS = ["https://example.com", "https://www.example.com"]
```

### Security headers

```python
SECURE_CONTENT_TYPE_NOSNIFF = True       # X-Content-Type-Options: nosniff (default on)
X_FRAME_OPTIONS = "DENY"                 # clickjacking protection
SECURE_REFERRER_POLICY = "same-origin"   # limit what is sent in the Referer header
SECURE_CROSS_ORIGIN_OPENER_POLICY = "same-origin"
```

For a **Content Security Policy (CSP)**, which restricts where scripts, styles, and images may load from, use a package such as `django-csp`. It is one of the strongest defenses against XSS.

### Cookie settings

```python
SESSION_COOKIE_HTTPONLY = True           # JavaScript cannot read it (default)
SESSION_COOKIE_SAMESITE = "Lax"          # reduces CSRF risk (default)
CSRF_COOKIE_SAMESITE = "Lax"
SESSION_COOKIE_AGE = 60 * 60 * 24 * 7    # one week instead of the two-week default
```

### Password and authentication settings

Keep `AUTH_PASSWORD_VALIDATORS` enabled and consider a higher minimum length (Lesson 12). Add rate limiting for login attempts with `django-axes`.

### Upload limits

```python
DATA_UPLOAD_MAX_MEMORY_SIZE = 5 * 1024 * 1024        # 5 MB for non-file request bodies
FILE_UPLOAD_MAX_MEMORY_SIZE = 2 * 1024 * 1024        # larger files spill to disk
DATA_UPLOAD_MAX_NUMBER_FIELDS = 1000
```

### Admin hardening

Restrict and monitor the admin (Lesson 9): use a non-default URL, two-factor authentication, and as few superusers as possible.

### Quick production checklist

| Setting | Production value |
|---|---|
| `DEBUG` | `False` |
| `ALLOWED_HOSTS` | Your domain names |
| `SECRET_KEY` | Long, random, loaded from the environment |
| `SECURE_SSL_REDIRECT` | `True` |
| `SESSION_COOKIE_SECURE` | `True` |
| `CSRF_COOKIE_SECURE` | `True` |
| `SECURE_HSTS_SECONDS` | Large, after testing |
| `SECURE_PROXY_SSL_HEADER` | Set if behind a proxy |
| `X_FRAME_OPTIONS` | `"DENY"` |

The next section explains how to verify these automatically.

---

## 17.3 Secrets management

A **secret** is any value that would cause harm if it became public: the Django `SECRET_KEY`, database passwords, API keys, cloud credentials, email passwords, and tokens.

### What the SECRET_KEY protects

Django uses `SECRET_KEY` to sign sessions, password-reset tokens, signed cookies, and other data. If an attacker learns it, they may be able to forge sessions or tokens and, in some configurations, escalate to running code on your server. It must be **unique** and **unguessable**.

### The golden rule

**Never commit secrets to version control.** Once a secret is pushed to a repository (even a private one, and even if you delete it later), it should be considered compromised, because Git history keeps it forever and repositories get shared, forked, and leaked. Automated bots scan public repositories for keys within minutes.

### Reading secrets from the environment

**Environment variables** keep secrets outside your code:

```python
# settings.py
import os

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]                 # fails loudly if missing
DEBUG = os.environ.get("DJANGO_DEBUG", "False") == "True"    # safe default
DATABASE_PASSWORD = os.environ["DB_PASSWORD"]
```

Using `os.environ["NAME"]` (square brackets) makes the app **crash at startup** when a secret is missing, which is better than silently running with a bad value. Environment variables are covered in more depth in Lesson 18.

### Generating a strong key

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Generate a **different** key for every environment (development, staging, production).

### Using a .env file for local development

Typing environment variables in your terminal every time is tedious. A `.env` file holds them for local use, loaded by a helper package such as `python-dotenv`, `django-environ`, or `python-decouple`:

```bash
# .env   (NEVER commit this file)
DJANGO_SECRET_KEY=change-me-to-a-long-random-string
DJANGO_DEBUG=True
DB_PASSWORD=local-dev-password
```

```python
# settings.py
from pathlib import Path
import environ

BASE_DIR = Path(__file__).resolve().parent.parent

env = environ.Env(DEBUG=(bool, False))
environ.Env.read_env(BASE_DIR / ".env")

SECRET_KEY = env("DJANGO_SECRET_KEY")
DEBUG = env("DJANGO_DEBUG")
```

Add it to `.gitignore`, and provide a safe template for teammates:

```
# .gitignore
.env
```

```bash
# .env.example   (safe to commit: contains placeholders only)
DJANGO_SECRET_KEY=
DJANGO_DEBUG=True
DB_PASSWORD=
```

### Secrets in production

Do not ship a `.env` file to the server. Instead, provide environment variables through:

| Option | Description |
|---|---|
| **Platform settings** | Heroku config vars, Render/Railway/Fly.io environment settings |
| **Container orchestration** | Docker/Kubernetes secrets |
| **Secret managers** | AWS Secrets Manager, Google Secret Manager, Azure Key Vault, HashiCorp Vault |
| **CI/CD secrets** | GitHub Actions encrypted secrets, GitLab CI variables |

### What to do if a secret leaks

1. **Rotate it immediately**: generate a new key, password, or token and revoke the old one.
2. Changing `SECRET_KEY` logs everyone out and invalidates outstanding password-reset links. That is acceptable and expected.
3. Review logs for suspicious activity.
4. Removing the secret from Git history is useful but **not** a substitute for rotation.

### Preventing accidents

- Use tools such as `git-secrets`, `gitleaks`, or GitHub's built-in secret scanning to catch committed secrets.
- Add pre-commit hooks that scan staged changes.
- Never paste secrets into issue trackers, chat, or screenshots.
- Never print secrets in logs or error messages.
- Give each service the **minimum permissions** it needs (for example, an S3 key that can only write to one bucket).

### Secrets and Django's DEBUG pages

With `DEBUG = True`, Django hides settings whose names contain words like `PASSWORD`, `SECRET`, `KEY`, or `TOKEN` on error pages. That is a safety net, not a reason to run with `DEBUG = True`.

---

## 17.4 Security checks

### Django's built-in check framework

Django can inspect your settings and warn about insecure configuration:

```bash
python manage.py check              # general problems
python manage.py check --deploy     # production security checklist
```

Always run `check --deploy` against your **production** settings:

```bash
DJANGO_SETTINGS_MODULE=mysite.settings.production python manage.py check --deploy
```

Example output:

```
System check identified some issues:

WARNINGS:
?: (security.W004) You have not set a value for the SECURE_HSTS_SECONDS setting...
?: (security.W008) Your SECURE_SSL_REDIRECT setting is not set to True...
?: (security.W012) SESSION_COOKIE_SECURE is not set to True...
?: (security.W018) You should not have DEBUG set to True in deployment.
```

Each warning has an ID (such as `security.W004`) that you can look up in the Django documentation. Fix them, or consciously silence one with `SILENCED_SYSTEM_CHECKS` if a warning does not apply (for example, when HSTS is handled by your proxy):

```python
SILENCED_SYSTEM_CHECKS = ["security.W004"]
```

Use `--fail-level WARNING` to make the command return an error exit code on warnings, which is ideal for CI:

```bash
python manage.py check --deploy --fail-level WARNING
```

### Keeping dependencies updated

Most real-world compromises come from **known vulnerabilities in outdated packages**, not from clever new attacks.

- Use a **supported Django release**. Django publishes security fixes only for supported versions, and you should upgrade promptly when a security release is announced.
- Pin your dependency versions in `requirements.txt` for repeatable builds.
- Regularly audit for known vulnerabilities:

```bash
pip install pip-audit
pip-audit -r requirements.txt
```

- Enable **Dependabot** or **Renovate** on your repository to receive automatic update pull requests.
- Subscribe to the Django security announcements (the `django-announce` mailing list).

### Static analysis

Tools that scan your own code for risky patterns:

```bash
pip install bandit
bandit -r . -x ./tests,./.venv
```

| Tool | Finds |
|---|---|
| `bandit` | Common Python security issues (hard-coded passwords, `eval`, weak crypto, SQL built from strings) |
| `pip-audit` / `safety` | Known vulnerabilities in installed packages |
| `gitleaks` / `git-secrets` | Secrets committed to Git |
| `ruff` / `flake8` | General code quality problems |

### Testing security rules

Write tests for the rules that matter most (Lesson 16):

```python
class AccessControlTests(TestCase):
    def test_anonymous_user_cannot_edit_post(self):
        post = PostFactory()
        response = self.client.post(reverse("blog:post_update", args=[post.pk]), {"title": "Hacked"})
        self.assertEqual(response.status_code, 302)       # sent to login
        post.refresh_from_db()
        self.assertNotEqual(post.title, "Hacked")

    def test_user_cannot_edit_someone_elses_post(self):
        post = PostFactory()
        other = UserFactory()
        self.client.force_login(other)
        response = self.client.post(reverse("blog:post_update", args=[post.pk]), {"title": "Hacked"})
        self.assertEqual(response.status_code, 403)
```

### Logging and monitoring

- Log failed logins, permission failures, and server errors (Lesson 18).
- Send unhandled exceptions to a service such as **Sentry** or by email to the `ADMINS`.
- Watch for unusual traffic, repeated 404s, or spikes in login failures.
- Keep logs free of passwords, tokens, and personal data.

### Backups and recovery

Security also means being able to recover. Take regular, **tested** database backups, store them separately from the server, and encrypt backups that contain personal data.

### A pre-launch security routine

1. Run `python manage.py check --deploy --fail-level WARNING` with production settings.
2. Confirm `DEBUG = False`, `ALLOWED_HOSTS`, and HTTPS settings.
3. Confirm all secrets come from the environment and none are in Git.
4. Run `pip-audit` and update vulnerable packages.
5. Verify permissions on every view that exposes or changes data.
6. Review the admin: URL, users, two-factor authentication.
7. Check backups and error monitoring.
8. Optionally, scan the deployed site with an external tool such as Mozilla Observatory.

---

[Previous](./[16]-Testing-Django-Applications.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[18]-Settings-And-Environments.md)
