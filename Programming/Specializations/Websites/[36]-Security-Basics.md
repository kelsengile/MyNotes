[Previous](./[35]-Browser-Compatibility.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md)

*Best Practices*

# Lesson 36 - Security Basics for Web Developers (CORS, CSRF, XSS)

## 36.1 CORS Revisited

As introduced in Lesson 5, **CORS (Cross-Origin Resource Sharing)** is a browser mechanism that blocks JavaScript on one origin from reading responses from another origin unless the server explicitly allows it, via headers like:

```
Access-Control-Allow-Origin: https://example.com
```

CORS protects users, not servers directly — it's enforced by the browser reading the response, not by the server refusing to process the request. Setting `Access-Control-Allow-Origin: *` allows any site to read responses, which is fine for public data but dangerous for anything user-specific or private.

---

## 36.2 XSS (Cross-Site Scripting)

**XSS** happens when an attacker manages to inject malicious JavaScript into a page that other users then load and execute — often through unescaped user input rendered directly into HTML:

```js
// Dangerous: renders raw HTML from user input
element.innerHTML = userComment;
```

If `userComment` contains `<script>stealCookies()</script>`, that script now runs in every visitor's browser who views it. The fix is to escape or sanitize any user-generated content before rendering it as HTML, and to prefer safer APIs like `textContent` over `innerHTML` when displaying plain text. Modern frameworks like React escape rendered content by default, which is a major reason XSS is less common in framework-based apps than in raw HTML manipulation.

---

## 36.3 CSRF (Cross-Site Request Forgery)

**CSRF** tricks a logged-in user's browser into unknowingly sending a request to a site they're authenticated with, by embedding that request on a different, malicious site (e.g. an auto-submitting form). Because cookies are sent automatically with matching-domain requests (Lesson 25), the victim's session cookie gets attached without their knowledge. Common defenses include CSRF tokens (a random value the server verifies matches what it issued, which an attacker's page can't know) and setting cookies with `SameSite=Strict` or `SameSite=Lax`, which stops browsers from sending them on cross-site requests at all.

---

## 36.4 SQL Injection

Referenced in Lesson 23, **SQL injection** happens when untrusted input is concatenated directly into a database query:

```js
// Dangerous
db.query(`SELECT * FROM users WHERE email = '${input}'`);
```

An attacker could set `input` to something like `' OR '1'='1`, altering the query's logic entirely. The fix is **parameterized queries**, where values are passed separately from the query structure and the database driver handles safe substitution:

```js
db.query("SELECT * FROM users WHERE email = ?", [input]);
```

---

## 36.5 A Few General Principles

- **Never trust client input** — validate and sanitize everything on the server, even if the front end already validates it (Lesson 22), since a client-side check can always be bypassed.
- **Keep secrets server-side** (Lesson 27) and never expose them in front-end code.
- **Use HTTPS everywhere** (Lesson 29) so data in transit can't be intercepted.
- **Keep dependencies updated** — vulnerabilities are regularly discovered and patched in third-party packages (Lesson 12); tools like `npm audit` flag known issues.
- **Apply the principle of least privilege** — a user, API key, or service should only have the access it strictly needs, limiting the damage if something is compromised.

Security isn't a single feature to add at the end — it's a set of habits applied consistently across every layer covered in this course, from how the front end renders data to how the database is queried.

---

[Previous](./[35]-Browser-Compatibility.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md)
