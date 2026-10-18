[Previous](./[12]-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[14]-Working-With-Databases.md)

*Tooling And Projects*

# Lesson 13 - Environment And Configuration

The same code often has to run on your laptop, a test server, and in production, each needing different settings such as ports, database addresses, and passwords. This lesson shows how to configure a program without changing its code.

## 13.1 Environment variables

**Environment variables** are named values provided by the operating system to a process. In Node.js they are available on `process.env`, always as strings.

```js
const port = Number(process.env.PORT) || 3000;
const debug = process.env.DEBUG === "true";
console.log(port, debug);
```

Setting them when starting a program:

```bash
# Linux / macOS
PORT=8080 node app.js

# Windows PowerShell
$env:PORT = "8080"; node app.js

# Windows cmd
set PORT=8080 && node app.js
```

Remember:

- Values are strings. `"false"` is a truthy string, so compare explicitly.
- A missing variable is `undefined`. Always provide a default or fail with a clear message.
- Common names: `NODE_ENV` (`development`, `production`, `test`), `PORT`, `DATABASE_URL`.

---

## 13.2 dotenv and config files

Typing variables every time is tedious. A **`.env` file** stores them in one place:

```
PORT=8080
DATABASE_URL=postgres://localhost:5432/mydb
API_KEY=abc123
```

Modern Node.js (20.6 and later) can load it with a flag, no library needed:

```bash
node --env-file=.env app.js
```

Or use the popular `dotenv` package:

```js
// npm install dotenv
require("dotenv").config();
console.log(process.env.PORT);
```

For larger structured settings, use a config file such as `config.json`, or a library like `config`. A good pattern is to read the environment in **one** file and export a validated object:

```js
// config.js
const required = ["DATABASE_URL"];
for (const key of required) {
  if (!process.env[key]) throw new Error(`Missing env var: ${key}`);
}

module.exports = {
  port: Number(process.env.PORT) || 3000,
  databaseUrl: process.env.DATABASE_URL,
  isProduction: process.env.NODE_ENV === "production",
};
```

This way your program fails immediately at startup if something is missing, rather than failing later in a confusing way.

---

## 13.3 Separating dev and production

Different environments need different behavior. `NODE_ENV` is the conventional switch.

```js
if (process.env.NODE_ENV === "production") {
  // concise logs, caching on, detailed errors hidden from users
} else {
  // verbose logs, helpful error pages
}
```

Typical differences:

| Setting | Development | Production |
|---|---|---|
| Logging | Verbose, colorful | Structured JSON |
| Error responses | Show stack traces | Generic message |
| Database | Local | Managed, backed up |
| Dependencies installed | All | Production only (`npm ci --omit=dev`) |
| HTTPS | Optional | Required |

Use separate env files per environment (`.env.development`, `.env.production`) or, better in production, set variables through your hosting platform. Keep the **code identical** across environments. Only the configuration should change.

---

## 13.4 Handling secrets

**Secrets** are values that must stay private: passwords, API keys, tokens, and encryption keys.

Rules:

- **Never commit secrets to Git.** Add `.env` to `.gitignore`.
- Commit a `.env.example` with fake placeholder values so others know which variables are needed.
- **Never hard-code secrets** in source files.
- **Never log secrets**, even in debug output.
- Use different secrets for development and production.
- If a secret is ever leaked, **rotate it** (create a new one and revoke the old one). Deleting it from Git history is not enough.
- In production, use your platform's secret storage (such as Docker/Kubernetes secrets, or a cloud secrets manager) instead of files on disk.

```
# .gitignore
.env
.env.*
!.env.example
```

[Previous](./[12]-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[14]-Working-With-Databases.md)