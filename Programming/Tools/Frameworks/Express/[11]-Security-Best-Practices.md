[Previous](./[10]-Views-Static-Files-And-Uploads.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[12]-Logging-And-Debugging.md)

*Hardening And Delivery*

# Lesson 11 - Security Best Practices

A working app is not a safe app. This lesson covers the most valuable protections for an Express server: secure HTTP headers, cross-origin rules, rate limiting, and cleaning or rejecting dangerous input. No single measure is enough. Layer them.

---

## 11.1 Helmet and secure headers

HTTP response headers can tell browsers to enable built-in protections. **Helmet** is middleware that sets a sensible group of them with one line.

```bash
npm install helmet
```

```js
import helmet from 'helmet';

app.use(helmet());   // register early, before your routes
```

### What Helmet sets

| Header | Protects against |
|---|---|
| `Content-Security-Policy` | Cross-site scripting (XSS) by limiting where scripts, styles, and images may load from |
| `Strict-Transport-Security` | Downgrade attacks, by telling browsers to use HTTPS only |
| `X-Content-Type-Options: nosniff` | Browsers guessing a file's type and running it as a script |
| `X-Frame-Options` / `frame-ancestors` | Clickjacking (your site embedded in a hidden frame) |
| `Referrer-Policy` | Leaking full URLs to other sites |
| `Cross-Origin-Opener-Policy` | Cross-window attacks |

Helmet also removes the `X-Powered-By: Express` header, which would otherwise advertise your framework (Lesson 3 showed `app.disable('x-powered-by')` for the same goal).

### Customizing the Content Security Policy

The default CSP is strict. If your pages load a script from a CDN, you must allow it:

```js
app.use(
  helmet({
    contentSecurityPolicy: {
      directives: {
        'script-src': ["'self'", 'https://cdn.jsdelivr.net'],
        'img-src': ["'self'", 'data:'],
      },
    },
  })
);
```

Tighten rather than disable. Turn a directive off (`contentSecurityPolicy: false`) only as a last resort.

### Pure JSON APIs

If your server only returns JSON, most headers still help and cost nothing. Keep Helmet on.

---

## 11.2 CORS

Browsers enforce the **same-origin policy**: a page from `https://app.example.com` cannot read responses from `https://api.example.com` unless the API opts in. The opt-in mechanism is **CORS** (Cross-Origin Resource Sharing).

An **origin** is the combination of scheme, host, and port. `http://localhost:3000` and `http://localhost:5173` are different origins.

> CORS is enforced by **browsers**, to protect users. It does not stop curl, scripts, or other servers from calling your API. It is not a replacement for authentication.

```bash
npm install cors
```

### Allow specific origins

```js
import cors from 'cors';

const allowedOrigins = ['https://app.example.com', 'http://localhost:5173'];

app.use(
  cors({
    origin: (origin, callback) => {
      // origin is undefined for non-browser clients such as curl or server-to-server calls
      if (!origin || allowedOrigins.includes(origin)) return callback(null, true);
      callback(new Error('Not allowed by CORS'));
    },
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization'],
    credentials: true,   // allow cookies on cross-origin requests
    maxAge: 600,         // browsers may cache the preflight answer for 10 minutes
  })
);
```

### Preflight requests

For "non-simple" requests (JSON bodies, an `Authorization` header, methods like `PUT` and `DELETE`), the browser first sends an `OPTIONS` request asking permission. The `cors` middleware answers it automatically, as long as you register it **before** your routes and authentication middleware.

### Rules of thumb

| Do | Avoid |
|---|---|
| List exact origins | `origin: '*'` on APIs that use cookies or sensitive data |
| Read the allowed origins from configuration | Hard-coding a dev origin into production |
| Set `credentials: true` only when you need cookies | Reflecting any incoming origin back blindly |

Browsers reject `credentials: true` combined with a wildcard origin, which is a helpful safety net.

---

## 11.3 Rate limiting

Without limits, one client can hammer your server, guess passwords by brute force, or run up your costs. **Rate limiting** caps how many requests a client may make in a time window.

```bash
npm install express-rate-limit
```

### A general limiter

```js
import { rateLimit } from 'express-rate-limit';

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,    // 15 minutes
  limit: 100,                  // max 100 requests per IP per window
  standardHeaders: 'draft-7',  // sends RateLimit-* headers so clients can adapt
  legacyHeaders: false,
  message: { error: 'Too many requests, please try again later' },
});

app.use('/api', apiLimiter);
```

When the limit is hit, the client receives `429 Too Many Requests`.

### A stricter limiter for sensitive routes

Login, registration, and password reset deserve much tighter limits:

```js
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  limit: 5,                    // 5 attempts per 15 minutes
  skipSuccessfulRequests: true,   // only failed attempts count
  message: { error: 'Too many login attempts' },
});

app.post('/auth/login', loginLimiter, loginHandler);
```

### Behind a proxy: set `trust proxy`

Rate limiting identifies clients by `req.ip`. If your app sits behind a load balancer or reverse proxy (Lesson 15), every request appears to come from the proxy's address, so everyone shares one limit. Tell Express to read the real client address:

```js
app.set('trust proxy', 1);   // trust exactly one proxy hop
```

Set the number to the actual count of proxies in front of your app. Trusting too many hops lets clients fake their IP through the `X-Forwarded-For` header.

### Multiple servers need a shared store

The default store keeps counters in memory, so each app instance counts separately and counters reset on restart. When you run several instances (see PM2 and Docker in Lesson 15), use a shared store such as Redis (for example the `rate-limit-redis` package).

### Slowing instead of blocking

Another tool is `express-slow-down`, which adds increasing delays instead of rejecting requests, useful for gentle abuse control.

---

## 11.4 Input sanitization

The central rule: **never trust input.** That includes the body, query string, route parameters, headers, cookies, and uploaded files. Protect yourself in two ways: **validate** (reject anything that does not match what you expect) and **escape or parameterize** (make sure data cannot be mistaken for code).

### Validate first

The schema validation from Lesson 7 is your strongest defense. Define exactly what is allowed and reject the rest.

```js
const createPostSchema = z.object({
  title: z.string().trim().min(1).max(200),
  body: z.string().max(10_000),
}).strict();   // reject unknown fields
```

Also limit body size so nobody can send enormous payloads:

```js
app.use(express.json({ limit: '100kb' }));
```

### SQL injection

SQL injection happens when user input is glued into a query string.

```js
// DANGEROUS: input becomes part of the SQL
db.query(`SELECT * FROM users WHERE email = '${req.body.email}'`);
// email = ' OR '1'='1   → returns every user
```

Always use **parameterized queries** or an ORM (Lesson 8). The database treats parameters as data, never as SQL:

```js
db.query('SELECT * FROM users WHERE email = $1', [req.body.email]);
```

### NoSQL injection

MongoDB queries are objects, so an attacker can send an object where you expected a string:

```js
// Body: { "email": "a@b.com", "password": { "$ne": "" } }
User.findOne({ email: req.body.email, password: req.body.password });
// "$ne" means "not equal to empty", so it matches any password!
```

Defend by validating types (a Zod `z.string()` rejects the object) and by hashing and comparing passwords as in Lesson 9. Never pass raw `req.body` or `req.query` straight into a query.

> **Express 5 note:** Older sanitizing packages such as `express-mongo-sanitize` and `xss-clean` work by modifying `req.query`, which is read-only in Express 5 (Lesson 7). They can throw errors or silently do nothing. Prefer schema validation, or check that a package explicitly supports Express 5 before using it.

### Cross-site scripting (XSS)

XSS means an attacker's text runs as JavaScript in other users' browsers. Defenses:

- **Escape output.** Template engines escape with `<%= %>` (Lesson 10). Front-end frameworks escape by default.
- **Do not build HTML by hand** from user input.
- **Use a Content Security Policy** (Helmet, section 11.1).
- **Use `httpOnly` cookies** so scripts cannot steal sessions (Lesson 3).
- If you must accept rich HTML (for example a comment editor), clean it with an allowlist-based sanitizer such as `sanitize-html` or `DOMPurify`, rather than trying to remove bad tags yourself.

### HTTP parameter pollution

Sending `?role=user&role=admin` makes `req.query.role` an **array**. Code that expects a string may behave unexpectedly. Validation that requires a string catches this.

### Path traversal

If you build file paths from user input, `../../etc/passwd` can escape your folder.

```js
import path from 'node:path';

const baseDir = path.resolve('files');

app.get('/files/:name', (req, res) => {
  const target = path.resolve(baseDir, req.params.name);

  if (!target.startsWith(baseDir + path.sep)) {
    return res.status(400).json({ error: 'Invalid file name' });
  }
  res.sendFile(target);
});
```

### Other essentials

| Practice | Why |
|---|---|
| Keep secrets in environment variables | Never commit them (Lesson 5) |
| Run `npm audit` regularly and update dependencies | Known vulnerabilities in packages are a common way in |
| Use HTTPS everywhere in production | Protects data and cookies in transit (Lesson 15) |
| Return generic errors in production | Stack traces help attackers (Lesson 7) |
| Give the database user only the permissions it needs | Limits damage if the app is compromised |
| Do not redirect to unchecked user-supplied URLs | Prevents open redirects (Lesson 3) |

### A secure baseline

```js
import express from 'express';
import helmet from 'helmet';
import cors from 'cors';
import { rateLimit } from 'express-rate-limit';

const app = express();

app.set('trust proxy', 1);
app.disable('x-powered-by');

app.use(helmet());
app.use(cors({ origin: allowedOrigins, credentials: true }));
app.use(rateLimit({ windowMs: 15 * 60 * 1000, limit: 100 }));
app.use(express.json({ limit: '100kb' }));

// routes (with schema validation), then 404 handler, then error handler
```

---

[Previous](./[10]-Views-Static-Files-And-Uploads.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[12]-Logging-And-Debugging.md)
