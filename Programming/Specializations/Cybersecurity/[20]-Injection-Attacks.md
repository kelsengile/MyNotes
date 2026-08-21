[Previous](./[19]-OWASP-Top-10-Overview.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[21]-Cross-Site-Scripting-and-CSRF.md)

*Web Application Security*

# Lesson 20 - Injection Attacks (SQL Injection, Command Injection)

## 20.1 What is Injection?

An **injection** vulnerability occurs when an application takes untrusted input (like a form field or URL parameter) and includes it in a command or query without properly separating data from instructions. The application ends up executing part of the attacker's input as code, rather than treating it purely as data. Injection has remained one of the most common and damaging web vulnerability categories for decades.

---

## 20.2 SQL Injection (SQLi)

**SQL Injection** occurs when user input is inserted directly into a SQL database query. Consider a simplified, vulnerable login query built by string concatenation:

```
SELECT * FROM users WHERE username = 'INPUT' AND password = 'INPUT2'
```

If the application doesn't properly handle special characters, an attacker entering `' OR '1'='1` as the username can change the query's logic entirely, potentially bypassing authentication because `'1'='1'` is always true. More advanced SQL injection can be used to read entire database tables, modify or delete data, or in some cases gain broader access to the underlying server.

**Defense:** Use **parameterized queries** (also called prepared statements), which keep user input strictly separated from the query structure, so input is always treated as data, never as executable SQL. Input validation and least-privilege database accounts (Lesson 13) provide additional layers of defense.

---

## 20.3 Command Injection

**Command injection** occurs when an application passes user input into a system shell command. For example, a poorly written network tool that runs `ping USERINPUT` could be manipulated by an attacker entering `8.8.8.8; rm -rf /` to chain an additional, unintended command after the legitimate one (shell metacharacters like `;`, `|`, and `&&` let attackers chain multiple commands).

**Defense:** Avoid passing user input to shell commands entirely where possible; when unavoidable, use language-provided APIs that pass arguments directly (without invoking a shell) rather than building a command string, and strictly validate/allow-list acceptable input.

---

## 20.4 Other Injection Types

- **NoSQL Injection** — a similar concept applied to NoSQL databases (like MongoDB), exploiting how queries are structured rather than SQL syntax specifically.
- **LDAP Injection** — manipulating queries to directory services like Active Directory.
- **XML/XXE (XML External Entity) Injection** — exploiting XML parsers configured to process external entity references, potentially exposing local files or internal systems.

---

## 20.5 General Defense Principles

Across all injection types, the same core principles apply: **never trust user input**, always validate and sanitize it, prefer APIs that inherently separate code from data (like parameterized queries), and apply least privilege so that even a successful injection has limited impact.

[Previous](./[19]-OWASP-Top-10-Overview.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[21]-Cross-Site-Scripting-and-CSRF.md)
