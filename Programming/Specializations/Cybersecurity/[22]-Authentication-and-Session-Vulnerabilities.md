[Previous](./[21]-Cross-Site-Scripting-and-CSRF.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[23]-API-Security-Basics.md)

*Web Application Security*

# Lesson 22 - Authentication & Session Vulnerabilities

## 22.1 Weak Authentication Practices

Authentication confirms a user's identity, usually via something they know (password), have (a security key or phone), or are (biometrics). Common weaknesses include:

- **Weak password policies** — allowing short, common, or easily guessed passwords.
- **No account lockout / rate limiting** — allowing unlimited login attempts, enabling brute-force or **credential stuffing** attacks (trying username/password pairs leaked from other breaches, betting on password reuse).
- **Verbose error messages** — telling an attacker "incorrect password" versus "user not found" reveals whether a given username exists at all, aiding reconnaissance.
- **Missing multi-factor authentication (MFA)** — relying on a password alone, when MFA (Lesson 36) dramatically reduces the impact of a stolen password.

---

## 22.2 What is a Session?

Because HTTP is stateless (each request is independent), web applications use **sessions** to remember that a user is logged in across multiple requests. After a successful login, the server issues a **session token**, usually stored in a cookie, which the browser automatically sends with every subsequent request to prove the user is authenticated.

---

## 22.3 Session-Related Vulnerabilities

- **Session hijacking** — an attacker steals a valid session token (e.g., via XSS or an unencrypted connection) and uses it to impersonate the victim without needing their password at all.
- **Session fixation** — an attacker tricks a victim into using a session ID the attacker already knows, so once the victim logs in, the attacker's known session ID becomes authenticated.
- **Predictable session tokens** — if session IDs are generated in a guessable pattern, an attacker may be able to guess a valid active session.
- **Missing session expiration** — sessions that never time out remain a stolen-token risk indefinitely, especially on shared or public computers.

---

## 22.4 Defending Authentication and Sessions

- Enforce strong password requirements and check new passwords against known-breached password lists.
- Implement account lockout or rate limiting on login attempts.
- Require and encourage MFA, especially for privileged accounts.
- Use the `Secure`, `HttpOnly`, and `SameSite` cookie attributes to protect session cookies from being sent over unencrypted connections, accessed by JavaScript (mitigating XSS-based theft), or sent cross-site (mitigating CSRF).
- Regenerate session IDs after login to prevent session fixation.
- Set reasonable session expiration and provide a clear logout function that properly invalidates the session server-side.

[Previous](./[21]-Cross-Site-Scripting-and-CSRF.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[23]-API-Security-Basics.md)
