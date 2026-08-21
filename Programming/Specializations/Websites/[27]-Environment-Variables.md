[Previous](./[26]-Connecting-Frontend-to-Backend.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[28]-Hosting-and-Deploying.md)

*Full-Stack Concepts*

# Lesson 27 - Environment Variables & Configuration

## 27.1 The Problem: Config That Shouldn't Be in Code

Every app needs configuration that differs between environments — a database URL on your laptop vs. in production, API keys, secret signing keys for tokens (Lesson 25). Hardcoding these directly into source files is dangerous (secrets get committed to Git and exposed publicly) and inflexible (changing a value means editing and redeploying code).

---

## 27.2 What Environment Variables Are

**Environment variables** are key-value pairs available to a running process from its operating environment, not from source code:

```js
const dbUrl = process.env.DATABASE_URL;
const port = process.env.PORT || 3000;
```

The same code can behave differently in different environments just by changing what's set outside the code itself.

---

## 27.3 .env Files for Local Development

Locally, developers typically keep environment variables in a `.env` file, loaded by a library like `dotenv`:

```
# .env
DATABASE_URL=postgres://localhost:5432/mydb
JWT_SECRET=super-secret-dev-key
```

```js
require("dotenv").config();
console.log(process.env.DATABASE_URL);
```

As mentioned in Lesson 14, `.env` must be listed in `.gitignore` — committing it would leak secrets into the repository's history, which is very difficult to fully remove afterward even if deleted in a later commit.

---

## 27.4 Providing Variables in Production

In production, environment variables are usually set through the hosting platform's dashboard or CLI rather than a `.env` file (Lesson 28 covers hosting in more depth). This keeps secrets out of the deployed codebase entirely and lets different environments (staging, production) use different values without any code changes.

---

## 27.5 Front-End Environment Variables Are Not Secret

An important distinction: environment variables bundled into front-end JavaScript (common with build tools like Vite, via `import.meta.env`) end up shipped in plain text to every visitor's browser. Anything genuinely secret — API keys with write access, signing keys — must stay on the server, never in front-end environment variables, regardless of how they're named.

---

[Previous](./[26]-Connecting-Frontend-to-Backend.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[28]-Hosting-and-Deploying.md)
