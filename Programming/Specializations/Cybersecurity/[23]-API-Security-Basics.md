[Previous](./[22]-Authentication-and-Session-Vulnerabilities.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[24]-Introduction-to-Malware-Types.md)

*Web Application Security*

# Lesson 23 - API Security Basics

## 23.1 Why APIs Need Dedicated Attention

**APIs (Application Programming Interfaces)**, especially REST and GraphQL APIs, now power most modern web and mobile applications, letting different systems communicate directly. Because APIs are often designed for machine-to-machine use, they can lack some of the built-in protections a traditional web page has (like a browser automatically escaping HTML), and are frequently exposed directly to the internet — making dedicated API security practices essential.

---

## 23.2 Broken Object Level Authorization (BOLA)

**BOLA**, sometimes called **IDOR (Insecure Direct Object Reference)**, is consistently one of the most common and impactful API vulnerabilities. It occurs when an API checks *that* a user is authenticated, but fails to check *whether* they're authorized to access the specific object they're requesting.

Example: an API endpoint `GET /api/orders/12345` returns order details. If the API only checks that the requester is logged in — not that order `12345` actually belongs to them — an attacker can simply change the ID to view other users' orders.

**Defense:** Every request for a specific object must verify that the authenticated user actually owns or is authorized to access that specific object, not just that they're logged in generally.

---

## 23.3 Authentication and Rate Limiting

- **API keys and tokens** (like OAuth 2.0 access tokens or JWTs) authenticate API requests; these must be transmitted only over HTTPS and never embedded in client-side code where they can be extracted.
- **Rate limiting** prevents abuse such as brute-force attacks, scraping, or resource exhaustion, by capping how many requests a client can make in a given time period.
- **Excessive data exposure** — APIs sometimes return more data than the client actually needs (relying on the client app to filter it), which can leak sensitive fields to anyone inspecting the raw API traffic.

---

## 23.4 Input Validation and Mass Assignment

APIs are just as vulnerable to injection (Lesson 20) as traditional web forms, and input validation matters equally here. A related, API-specific issue is **mass assignment**: if an API automatically maps incoming JSON fields directly to internal data objects, an attacker might include an unexpected field (like `"isAdmin": true`) in a request, and if the backend isn't carefully restricting which fields can be set this way, the attacker could grant themselves elevated privileges.

---

## 23.5 API Security Checklist

- Enforce strong authentication and object-level authorization on every endpoint.
- Use HTTPS everywhere; never expose API keys in client-side code or public repositories.
- Apply rate limiting and monitor for abnormal usage patterns.
- Return only the data the client actually needs.
- Explicitly define which fields can be set via input, rather than trusting the entire payload.
- Maintain and enforce API documentation (like an OpenAPI spec) so undocumented, forgotten "shadow" endpoints don't slip through unmonitored.

[Previous](./[22]-Authentication-and-Session-Vulnerabilities.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[24]-Introduction-to-Malware-Types.md)
