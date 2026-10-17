[Previous](./[14]-Working-With-Databases.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[16]-Testing.md)

*Building The Backend*

# Lesson 15 - Authentication And Security

Security is not a feature you add at the end; it is part of how every layer of an application is built. The good news is that frameworks include protections for many common problems, as long as you use them as intended. This lesson explains how applications identify users and decide what they may do, how login state is kept between requests, the most common attacks, and the practices that keep you safe.

## 15.1 Authentication vs Authorization

These two words sound alike and are constantly confused, but they answer different questions.

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| **Question** | *Who are you?* | *What are you allowed to do?* |
| **Happens** | First | After authentication |
| **Example** | Logging in with a password | Only admins may delete users |
| **Failure response** | `401 Unauthorized` | `403 Forbidden` |

An analogy: showing your ID at the entrance of a building is *authentication*. The key card that opens only certain floors is *authorization*.

### Authentication

Common ways to prove identity:

- **Something you know**: a password.
- **Something you have**: a phone that receives a code, or a hardware key.
- **Something you are**: a fingerprint or face scan.
- **Combinations of these (multi-factor authentication, MFA)** are much stronger than a password alone.

Applications may also let users sign in through another provider ("Sign in with Google"). These use standards such as **OAuth 2.0** and **OpenID Connect**, so you never have to handle the user's password at all.

### Storing passwords correctly

> **Never store passwords in plain text, and never encrypt them reversibly.** Store a **hash**.

A **hash function** turns a password into a fixed-length scrambled value that cannot practically be reversed. At login, you hash the entered password and compare it with the stored hash. Good password hashing uses two extra ideas:

- A random **salt** per user, so two users with the same password get different hashes.
- A deliberately **slow** algorithm (such as bcrypt, scrypt, or Argon2), so attackers cannot try billions of guesses per second. General-purpose hashes like plain SHA-256 are *too fast* for this job.

Here is the idea in a small demonstration using Python's built-in scrypt:

```python
import hashlib
import hmac
import os


def hash_password(password: str) -> str:
    salt = os.urandom(16)  # a random salt makes identical passwords hash differently
    digest = hashlib.scrypt(password.encode(), salt=salt, n=2**14, r=8, p=1)
    return salt.hex() + ":" + digest.hex()


def verify_password(password: str, stored: str) -> bool:
    salt_hex, digest_hex = stored.split(":")
    digest = hashlib.scrypt(
        password.encode(), salt=bytes.fromhex(salt_hex), n=2**14, r=8, p=1
    )
    return hmac.compare_digest(digest.hex(), digest_hex)


stored = hash_password("correct horse battery staple")
print(verify_password("correct horse battery staple", stored))
print(verify_password("wrong password", stored))
```

Output:

```text
True
False
```

> This example is for understanding only. In real applications, **use your framework's built-in password handling** (Django, Laravel, Spring Security, and ASP.NET Core Identity all provide one) or a well-reviewed library. Do not invent your own.

### Authorization

Once you know who the user is, you decide what they may do. Common models:

| Model | Idea | Example |
|---|---|---|
| **Role-based (RBAC)** | Users have roles; roles have permissions | `admin`, `editor`, `viewer` |
| **Permission-based** | Individual rights are assigned directly | "can_delete_posts" |
| **Ownership rules** | Users may act only on their own data | You may edit *your* profile, not others' |

Authorization checks belong **on the server, on every request**. Hiding a button in the interface is not security.

---

## 15.2 Sessions, Cookies, and Tokens (JWT)

Because HTTP is stateless (Lesson 12), after you log in the server must have a way to recognize you on your *next* request. There are two main approaches.

### Cookies

A **cookie** is a small piece of data the server asks the browser to store and automatically send back with every request to that site. Cookies are the standard carrier for login state in browsers.

### Approach 1: Server-side sessions

1. The user logs in.
2. The server creates a **session** record (stored in memory, a database, or Redis) and generates a random **session ID**.
3. The session ID is sent to the browser in a cookie.
4. On each request, the browser sends the cookie; the server looks up the session and knows who you are.

```text
Browser                               Server
  │── login (user + password) ───────►│  create session #a91f..., store user id
  │◄── Set-Cookie: session=a91f... ───│
  │── GET /profile (Cookie: a91f...) ►│  look up session #a91f... → user 7
  │◄── 200 OK (your profile) ─────────│
```

Sessions are simple and easy to **revoke**: delete the server-side record and the user is logged out immediately. The cost is that the server must store session data, which needs shared storage once you run several servers.

### Approach 2: Tokens and JWT

With **token-based** authentication, the server gives the client a signed **token** at login, and the client sends it back with each request, usually in an `Authorization: Bearer <token>` header. The server verifies the signature instead of looking anything up.

The most common token format is the **JWT (JSON Web Token)**. A JWT has three parts, separated by dots:

```text
header . payload . signature
```

- The **header** names the signing algorithm.
- The **payload** carries *claims*, such as the user's id, role, and expiry time.
- The **signature** proves the token was created by someone who knows the secret key and was not altered.

Let's build one by hand to see what is inside:

```python
import base64
import hashlib
import hmac
import json


def b64url(data: bytes) -> str:
    return base64.urlsafe_b64encode(data).rstrip(b"=").decode()


def b64url_decode(text: str) -> bytes:
    return base64.urlsafe_b64decode(text + "=" * (-len(text) % 4))


secret = b"demo-secret"
header = b64url(json.dumps({"alg": "HS256", "typ": "JWT"}).encode())
payload = b64url(json.dumps({"sub": "42", "role": "admin"}).encode())
signature = b64url(hmac.new(secret, f"{header}.{payload}".encode(), hashlib.sha256).digest())

token = f"{header}.{payload}.{signature}"
print(token)

# Anyone can read the payload: a JWT is signed, not encrypted
print(json.loads(b64url_decode(token.split(".")[1])))
```

Output:

```text
eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiAiNDIiLCAicm9sZSI6ICJhZG1pbiJ9.sOfMhLXlWxAIj6dDAE35a5-B7r02xso6RCgnt7RfpuU
{'sub': '42', 'role': 'admin'}
```

The key lesson is in the last line: **anyone holding the token can read its payload.** A JWT is **signed, not encrypted**. It guarantees the contents were not *tampered with*, but not that they are *secret*. Never put passwords or sensitive information in a JWT.

### Sessions vs tokens

| | Server-side sessions | JWT tokens |
|---|---|---|
| **Where state lives** | On the server | Inside the token itself |
| **Server lookup per request** | Yes | No, just verify the signature |
| **Revoking access** | Easy: delete the session | Hard: a token is valid until it expires |
| **Works across many servers** | Needs shared session storage | Naturally stateless |
| **Typical use** | Traditional websites | APIs, mobile apps, service-to-service calls |

Neither is universally better. Many applications are well served by plain sessions.

### Short-lived tokens and refresh tokens

Because tokens are hard to revoke, they are usually made **short-lived** (minutes). A longer-lived **refresh token** (stored more carefully and revocable on the server) lets the client request a new short-lived **access token** without logging in again.

### Where to keep the credential in a browser

| Storage | Risk |
|---|---|
| **`localStorage`** | Readable by any JavaScript on the page, so a cross-site scripting flaw (15.3) can steal it |
| **Cookie with the `HttpOnly` flag** | Not readable by JavaScript, which protects against theft through XSS, but requires protection against CSRF (15.3) |

A widely recommended default for browser apps is a cookie marked `HttpOnly`, `Secure`, and `SameSite`:

| Cookie flag | Effect |
|---|---|
| **HttpOnly** | JavaScript cannot read the cookie |
| **Secure** | The cookie is only sent over HTTPS |
| **SameSite** | Controls whether the cookie is sent on requests triggered from other sites, limiting CSRF |

Mobile apps do not have cookies in the same way and typically store tokens in the platform's secure storage (the Keychain on iOS, the Keystore on Android).

---

## 15.3 Common Vulnerabilities (XSS, CSRF, Injection)

Most successful attacks use a small set of well-known weaknesses. The **OWASP Top 10** (owasp.org/www-project-top-ten) is a respected, regularly updated list of the most critical web application risks and is excellent further reading. Here are the ones every developer must understand.

### SQL injection

**What it is:** The attacker supplies input that changes the *meaning* of your database query, because you pasted that input directly into SQL text.

This program shows both the vulnerable and the safe way, with an attacker's input of `' OR '1'='1`:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE users (name TEXT, password TEXT)")
conn.execute("INSERT INTO users VALUES ('ada', 's3cret')")

name, password = "ada", "' OR '1'='1"  # attacker-controlled input

# VULNERABLE: user input is pasted into the SQL text
unsafe = f"SELECT * FROM users WHERE name = '{name}' AND password = '{password}'"
print("Unsafe:", conn.execute(unsafe).fetchall())

# SAFE: the driver sends the values separately from the SQL
safe = "SELECT * FROM users WHERE name = ? AND password = ?"
print("Safe:  ", conn.execute(safe, (name, password)).fetchall())
```

Output:

```text
Unsafe: [('ada', 's3cret')]
Safe:   []
```

In the unsafe version, the attacker's input turned the condition into "password is empty-string **or** 1 equals 1", which is always true, and logged in without knowing the password. In the safe version, the value is treated purely as *data*, so nothing matches.

**Defense:** Always use **parameterized queries** (placeholders). ORMs do this for you in normal use. Never assemble SQL by joining strings with user input. The same idea applies to other "injection" types, such as operating system commands and NoSQL queries: never mix untrusted data into code.

### Cross-Site Scripting (XSS)

**What it is:** The attacker gets their own JavaScript to run in *other users'* browsers by getting your site to display it. For example, a malicious comment containing a `<script>` tag that, if shown unescaped, steals other visitors' sessions or performs actions as them.

**Defense:** **Escape output.** Convert special characters so the browser shows them as text rather than running them:

```python
import html

comment = '<script>alert("hacked")</script>'
print(html.escape(comment))
```

Output:

```text
&lt;script&gt;alert(&quot;hacked&quot;)&lt;/script&gt;
```

Modern frameworks, such as React, Angular, Vue.js, Django templates, Blade, and Razor, **escape output automatically** by default. The danger comes when you deliberately bypass that, using features named things like `dangerouslySetInnerHTML`, `v-html`, `|safe`, or `{!! !!}`. Avoid those with any content that came from users. Additional protections include a **Content Security Policy (CSP)** header, which limits which scripts a page may run, and `HttpOnly` cookies.

### Cross-Site Request Forgery (CSRF)

**What it is:** Because browsers automatically attach cookies to requests, a malicious site can trick a logged-in user's browser into sending a request to your site (such as "transfer money") that your server believes is genuine.

**Defense:**

- Set the `SameSite` attribute on session cookies, which is the default in modern browsers for many cases.
- Use **CSRF tokens**: a secret, per-session value that forms must include and the server verifies. Frameworks such as Django, Laravel, Rails, Spring Security, and ASP.NET Core provide this built in. Keep it enabled.
- Never perform state-changing actions with `GET` requests.

APIs that authenticate with an `Authorization` header (rather than cookies) are not vulnerable to CSRF in the same way, because browsers do not attach that header automatically.

### Broken access control

**What it is:** The server fails to check that the user is allowed to access a resource. A classic form is changing `/orders/101` to `/orders/102` in the URL and seeing someone else's order.

**Defense:** On **every** request, verify that the current user is allowed to act on **that specific** record. Do not rely on ids being hard to guess.

### Other common problems

| Problem | Short description | Defense |
|---|---|---|
| **Sensitive data exposure** | Secrets, passwords, or personal data leaked in logs, errors, or repositories | Keep secrets in the environment, never commit them, and avoid logging them |
| **Vulnerable dependencies** | Known flaws in packages you use | Audit and update regularly (Lesson 5) |
| **Security misconfiguration** | Debug mode in production, default passwords, overly open settings | Use production settings, review configuration |
| **Insecure deserialization** | Loading untrusted data into objects in an unsafe way | Use safe formats such as JSON, and validate |
| **Missing rate limiting** | Attackers can try unlimited passwords | Add rate limiting and lockouts (Lesson 13) |

---

## 15.4 Security Best Practices

Security is built from many small, consistent habits. Use this as a working checklist.

### Protect data in transit and at rest

- **Use HTTPS everywhere**, including in development when practical, and redirect all HTTP traffic to HTTPS.
- **Hash passwords** with a slow, salted algorithm provided by your framework.
- **Encrypt sensitive data** that you must store, and avoid collecting data you do not need.

### Never trust input

- **Validate on the server**, always (Lesson 13).
- **Use parameterized queries** or an ORM.
- **Escape output**, and keep your framework's automatic escaping turned on.

### Control access

- **Check authorization on every request**, on the server.
- Apply the **principle of least privilege**: give users, services, and database accounts only the permissions they actually need.
- Offer **multi-factor authentication** where possible.
- Give sessions and tokens a sensible **expiry**, and support logout.

### Manage secrets carefully

- Keep secrets in **environment variables or a secrets manager**, never in code or version control.
- Use **different secrets** for development and production.
- **Rotate** any secret that may have been exposed.

### Reduce the attack surface

- **Keep dependencies updated** and audit them (Lesson 5).
- Add **security headers** and rate limiting through middleware (Lesson 13).
- Keep **CORS** allowed origins as narrow as possible.
- **Turn off debug mode** and detailed error pages in production.
- Return **generic error messages** to users, and log details privately.

### Stay observant

- **Log security-relevant events** such as failed logins and permission failures, without logging the secrets themselves.
- **Monitor** for unusual activity.
- **Review** your code and configuration for these issues regularly, and stay familiar with the OWASP Top 10.

### The mindset

No application is perfectly secure, so security is about **layers**: validation, escaping, access checks, least privilege, monitoring, and updates all work together, so that when one layer fails, another still protects you. The most important single habit is to **use the security features your framework already provides, and not to disable them** just because they are inconvenient.

---

[Previous](./[14]-Working-With-Databases.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[16]-Testing.md)
