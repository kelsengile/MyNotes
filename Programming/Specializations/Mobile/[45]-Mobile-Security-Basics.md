[Previous](./%5B44%5D-Performance-Optimization%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[46]-Battery-and-Resource-Efficiency.md)

*Best Practices*

# Lesson 45 - Mobile Security Basics

## 45.1 Secure Storage of Sensitive Data

Sensitive data (auth tokens, passwords, personal info) should never be stored in plain text files or standard preferences/UserDefaults, since those are relatively easy to read on a compromised or rooted/jailbroken device. Instead, use platform-provided secure storage:

- **iOS** — the **Keychain**, which stores small pieces of data encrypted and tied to the device/app.
- **Android** — the **Keystore** system (often paired with `EncryptedSharedPreferences`), which similarly encrypts stored values.

---

## 45.2 Network Security

All network traffic should use **HTTPS/TLS**, never plain HTTP, so data can't be read or altered in transit. Both platforms now enforce this by default:

- **iOS** — **App Transport Security (ATS)** blocks plain HTTP connections unless explicitly (and narrowly) allowed.
- **Android** — apps targeting recent SDK versions block cleartext (HTTP) traffic by default via the network security configuration.

For especially sensitive apps, **certificate pinning** goes a step further — hardcoding the expected server certificate so the app rejects even a validly-signed but unexpected certificate, defending against certain man-in-the-middle attacks.

---

## 45.3 Authentication and Tokens

Most apps authenticate using tokens (e.g. **OAuth 2.0** access/refresh tokens) rather than sending a username/password on every request. Access tokens should be short-lived, refreshed via a longer-lived refresh token, and stored in secure storage (never in plain preferences or hardcoded in the app itself).

---

## 45.4 Protecting Source Code and Secrets

- **Never hardcode API keys or secrets** directly in client-side code — even in a compiled app, strings and logic can be extracted through reverse engineering (decompiling an APK or inspecting an IPA). Sensitive operations (e.g. anything requiring a secret key) belong on a backend server, not the client.
- **Obfuscation** (e.g. Android's **R8/ProGuard**) makes reverse engineering harder by renaming classes/methods to meaningless names, though it doesn't make code truly unreadable — it raises the cost of tampering rather than eliminating it entirely.

---

## 45.5 Input Validation

Just as with any application, never trust data coming from the client alone — always re-validate on the backend, since a modified or jailbroken app can bypass any client-side checks (validation, permission checks, in-app purchase state) entirely.

[Previous](./%5B44%5D-Performance-Optimization%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[46]-Battery-and-Resource-Efficiency.md)
