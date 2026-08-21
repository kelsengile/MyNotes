[Previous](./[44]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[46]-Crash-Reporting-and-Analytics.md)

*Best Practices*

# Lesson 45 - Desktop App Security Basics

## 45.1 The Desktop Threat Model

Unlike a sandboxed web page, a desktop app often runs with the full privileges of the user who launched it — meaning a vulnerability can potentially read/write any file the user can, or run arbitrary commands. This makes input validation, dependency hygiene (Lesson 33), and least-privilege design more consequential than in a browser context.

---

## 45.2 Securing Multi-Process Frameworks

In Electron/Tauri-style apps, the renderer (UI) process should never have unrestricted access to the filesystem, shell, or Node.js APIs — enable context isolation and a minimal `preload` bridge (Lesson 29) that exposes only the specific operations the UI actually needs, rather than the whole system API surface. Loading remote or untrusted web content inside a privileged renderer is a common and serious mistake.

---

## 45.3 Handling Untrusted Input

Treat any data from outside the app's own trusted code as untrusted: files a user opens, data pasted from the clipboard, content from a plugin, or a response from a network call. Validate and sanitize before acting on it — a document parser encountering a malformed file should fail gracefully, not crash or execute unintended code, and file paths derived from user input should be checked against path traversal (`../../etc/passwd`-style attacks).

---

## 45.4 Secrets and Credentials

Never embed API keys or secrets directly in shipped source code — they're trivially extractable from any installed binary. Store secrets server-side where possible, request short-lived tokens instead of long-lived credentials, and use the OS credential store (Lesson 20) for anything that must live on the user's machine. Keep dependencies patched (Lesson 33), since outdated libraries are one of the most common real-world entry points for compromise.

[Previous](./[44]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[46]-Crash-Reporting-and-Analytics.md)
