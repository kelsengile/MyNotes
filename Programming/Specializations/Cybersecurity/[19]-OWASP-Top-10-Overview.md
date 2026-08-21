[Previous](./[18]-Common-Cryptographic-Pitfalls.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[20]-Injection-Attacks.md)

*Web Application Security*

# Lesson 19 - OWASP Top 10 Overview

## 19.1 What is OWASP?

**OWASP (Open Worldwide Application Security Project)** is a nonprofit foundation that produces free, widely respected resources on web application security. Its best-known publication, the **OWASP Top 10**, is a regularly updated list of the most critical web application security risks, based on real-world data and expert consensus. It's used across the industry as a baseline checklist for both developers and security testers.

---

## 19.2 The Categories (Representative List)

While the exact list and wording evolve with each update, the Top 10 has consistently covered categories such as:

1. **Broken Access Control** — users able to act outside their intended permissions (e.g., viewing another user's data by changing an ID in a URL).
2. **Cryptographic Failures** — sensitive data exposed due to weak or missing encryption (Lesson 18).
3. **Injection** — untrusted input executed as code or commands (Lesson 20).
4. **Insecure Design** — security flaws baked into the application's architecture, not fixable by a simple patch.
5. **Security Misconfiguration** — default settings, unnecessary features, or verbose error messages left exposed.
6. **Vulnerable and Outdated Components** — using libraries or frameworks with known, unpatched vulnerabilities.
7. **Identification and Authentication Failures** — weaknesses in login and session handling (Lesson 22).
8. **Software and Data Integrity Failures** — trusting data or code (like software updates) without verifying its integrity.
9. **Security Logging and Monitoring Failures** — insufficient logging making it hard to detect or investigate an attack.
10. **Server-Side Request Forgery (SSRF)** — tricking a server into making unintended requests on the attacker's behalf, often to reach internal-only systems.

---

## 19.3 Why This List Matters

The Top 10 isn't an exhaustive list of every possible vulnerability — it's a prioritized view of the risks that appear most often and cause the most damage in practice. It's used to:

- Guide **secure code reviews** and **developer training**.
- Structure **penetration testing** scope and methodology.
- Provide a shared vocabulary between developers, testers, and management.
- Serve as a baseline requirement in many compliance frameworks and vendor security questionnaires.

---

## 19.4 How to Use It as a Learning Tool

Rather than memorizing the list, focus on understanding *why* each category is dangerous and what a secure design looks like instead. The following lessons in this Topic go deeper into several of the most common and impactful categories: injection (Lesson 20), XSS/CSRF (Lesson 21), authentication and session issues (Lesson 22), and API-specific concerns (Lesson 23).

[Previous](./[18]-Common-Cryptographic-Pitfalls.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[20]-Injection-Attacks.md)
