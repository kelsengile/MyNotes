[Previous](./[12]-HTTP-And-REST-APIs.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[14]-Working-With-Databases.md)

*Building The Backend*

# Lesson 13 - Middleware And Request Handling

Between "a request arrives" and "your handler runs" there is a lot of work that has nothing to do with your specific feature: logging the request, reading its body, checking who is calling, enforcing limits, and more. Frameworks handle this shared work with **middleware**. This lesson explains how middleware works, how a request flows through it, how to validate input and handle errors, and which middleware you will meet most often.

## 13.1 What Is Middleware

**Middleware** is code that sits *between* the incoming request and your final handler (and, on the way back, between your handler and the outgoing response). Each piece of middleware does one focused job, then either passes the request along to the next piece or stops the process and responds itself.

A helpful analogy is airport security. Before you reach your gate, you pass through several checkpoints in order: check-in, ID check, baggage scan. Each checkpoint either lets you continue or stops you. Your final destination (the gate) is like the handler, and the checkpoints are middleware.

```text
Request ─► [Logging] ─► [Parse body] ─► [Authentication] ─► Handler
                                                              │
Response ◄── [Logging] ◄── [Compression] ◄──────────────────┘
```

### What middleware is good for

Middleware handles **cross-cutting concerns**: tasks that apply to many or all routes and would otherwise be copied into every handler.

| Task | Why middleware fits |
|---|---|
| Logging every request | Same code for every route |
| Parsing JSON or form bodies | Needed before any handler can read the data |
| Authentication | Check credentials before sensitive routes run |
| Rate limiting | Protect all routes from abuse |
| Adding security headers | Same headers on every response |
| Handling errors consistently | One place to turn failures into proper responses |

### Middleware across frameworks

The idea exists in nearly every backend framework, sometimes under another name:

| Framework | Where you meet it |
|---|---|
| **Express** | `app.use(fn)` and route-level functions |
| **Django** | The `MIDDLEWARE` list in `settings.py` |
| **Laravel** | Middleware classes registered on routes or groups |
| **FastAPI / Starlette** | `app.add_middleware(...)` |
| **ASP.NET Core** | `app.Use...` calls forming the request pipeline |
| **Spring Boot** | Servlet filters and interceptors |
| **NestJS** | Middleware, guards, interceptors, and pipes |

Learn it once and you will recognize it everywhere.

---

## 13.2 Request and Response Lifecycle

### The pipeline

Middleware forms a **pipeline**: an ordered list. Each function receives the request, the response, and a `next` function. Calling `next()` hands control to the following piece. *Not* calling it ends the chain.

Here is a tiny pipeline in plain JavaScript so you can see exactly how this works:

```javascript
function run(middlewares, req, res) {
  let index = 0;
  function next() {
    const middleware = middlewares[index++];
    if (middleware) middleware(req, res, next);
  }
  next();
}

const logger = (req, res, next) => {
  console.log("logger: before");
  next();
  console.log("logger: after");
};

const auth = (req, res, next) => {
  console.log("auth: before");
  next();
  console.log("auth: after");
};

const handler = () => console.log("handler: runs last");

run([logger, auth, handler], {}, {});
```

Output:

```text
logger: before
auth: before
handler: runs last
auth: after
logger: after
```

Study the order carefully. Code *before* `next()` runs on the way **in**, in list order. Code *after* `next()` runs on the way **out**, in **reverse** order, after the handler has finished. This is sometimes called the **onion model**, because each layer wraps the ones inside it:

```text
┌───────────────── logger ─────────────────┐
│  ┌────────────── auth ───────────────┐   │
│  │                                   │   │
│  │            handler                │   │
│  │                                   │   │
│  └───────────────────────────────────┘   │
└──────────────────────────────────────────┘
   request travels inward, response travels outward
```

This is how a logger can measure how long a request took: it records the time before `next()` and calculates the difference after.

### Order matters

Because middleware runs in the order it is registered, the order is part of your design:

- A **body parser** must come *before* anything that reads the body.
- **Authentication** must come *before* the handlers it protects.
- **Error-handling** middleware usually goes *last*, so it can catch problems from everything before it.

A very common beginner bug is registering middleware in the wrong order and wondering why `req.body` is empty or a protected route is open.

### Ending the chain early

A middleware can **short-circuit** the pipeline by sending a response without calling `next()`. For example, an authentication check that finds no valid credentials can respond with `401 Unauthorized` immediately, and the handler never runs. The sections below show this in a real example.

### Scope: global vs route-level

| Scope | Applies to | Example |
|---|---|---|
| **Global** | Every request | Logging, body parsing |
| **Group** | A set of related routes | All routes under `/admin` |
| **Route-level** | One route only | A check that only the "create user" route needs |

---

## 13.3 Validation and Error Handling

### Never trust incoming data

Anything that arrives from outside, such as form fields, JSON bodies, query strings, and headers, can be missing, malformed, or deliberately malicious. **Validate input at the boundary of your application**, before it reaches your business logic or your database.

Validation typically checks:

- **Presence**: required fields exist.
- **Type**: a number is really a number.
- **Format**: an email looks like an email.
- **Range and length**: an age is between 0 and 150; a name is not empty.

When validation fails, respond with `400 Bad Request` or `422 Unprocessable Content` and a clear message (Lesson 12).

> Validation in the **browser** is for user convenience (instant feedback). It can always be bypassed, so validation on the **server** is mandatory.

### Validation libraries

You rarely write validation by hand for every field. Most ecosystems have well-tested tools, usually based on a declared **schema**:

| Ecosystem | Examples |
|---|---|
| JavaScript / TypeScript | Zod, Joi, class-validator (NestJS) |
| Python | Pydantic (used by FastAPI), Django forms and serializers |
| Java | Bean Validation annotations in Spring Boot |
| PHP | Laravel's built-in validator |
| C# | Data annotations, FluentValidation |

Here is a schema-based approach using Pydantic. It describes what valid data looks like, and reports every problem found:

```python
from pydantic import BaseModel, Field, ValidationError


class NewUser(BaseModel):
    name: str = Field(min_length=1)
    age: int = Field(ge=0, le=150)


try:
    NewUser(name="", age="abc")
except ValidationError as error:
    for problem in error.errors():
        print(problem["loc"], "-", problem["msg"])
```

Output:

```text
('name',) - String should have at least 1 character
('age',) - Input should be a valid integer, unable to parse string as an integer
```

### A complete example with Express

The following server combines the ideas from this lesson: a logging middleware, an authentication-style check that can stop the chain, manual validation, and a central error handler.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

// Logging middleware: runs for every request
app.use((req, res, next) => {
  const start = Date.now();
  res.on("finish", () => {
    console.log(`${req.method} ${req.originalUrl} -> ${res.statusCode} (${Date.now() - start}ms)`);
  });
  next();
});

// Route-level middleware: can stop the chain
function requireApiKey(req, res, next) {
  if (req.get("x-api-key") !== "secret") {
    return res.status(401).json({ error: "Missing or invalid API key" });
  }
  next();
}

app.post("/users", requireApiKey, (req, res) => {
  const { email } = req.body ?? {};
  if (typeof email !== "string" || !email.includes("@")) {
    return res.status(400).json({ error: "A valid email is required" });
  }
  res.status(201).json({ email });
});

app.get("/boom", () => {
  throw new Error("Something broke");
});

// Error-handling middleware: recognised by its four parameters
app.use((err, req, res, next) => {
  console.error("Unhandled error:", err.message);
  res.status(500).json({ error: "Internal server error" });
});

app.listen(3000);
```

Here is what happens when we call it.

A request without the API key is stopped by `requireApiKey`:

```bash
curl -X POST http://localhost:3000/users -H "Content-Type: application/json" \
  -d '{"email":"ada@example.com"}'
```

```text
{"error":"Missing or invalid API key"}   [401]
```

With the key but invalid data, validation rejects it:

```bash
curl -X POST http://localhost:3000/users -H "x-api-key: secret" \
  -H "Content-Type: application/json" -d '{"email":"nope"}'
```

```text
{"error":"A valid email is required"}   [400]
```

With the key and valid data, it succeeds:

```text
{"email":"ada@example.com"}   [201]
```

And a route that throws an unexpected error reaches the error handler:

```text
{"error":"Internal server error"}   [500]
```

The server's console shows the logger's output, plus the error message:

```text
POST /users -> 401 (4ms)
POST /users -> 400 (5ms)
POST /users -> 201 (0ms)
Unhandled error: Something broke
GET /boom -> 500 (1ms)
```

(The millisecond values will vary on your machine.)

### Centralized error handling

Notice the final `app.use((err, req, res, next) => ...)` in the example. In Express, a middleware with **four parameters** is an *error handler*, and any error thrown in a handler is routed to it. Most frameworks offer an equivalent: exception filters in NestJS, `@ControllerAdvice` in Spring Boot, exception handlers in FastAPI and Django, and the exception handler in Laravel.

Centralizing error handling has several benefits:

- **Consistency**: every failure produces the same response shape.
- **Safety**: you control what clients see.
- **Less repetition**: handlers do not need their own try/catch for everything.

### Error-handling rules

1. **Never leak internals.** Do not send stack traces, SQL errors, or file paths to the client. Notice that the example returns a generic message to the client but logs the real error on the server.
2. **Use accurate status codes.** `4xx` means the client should fix something; `5xx` means the server failed.
3. **Log the details** so you can debug later (see the next section).
4. **Distinguish expected from unexpected errors.** "Task not found" is an expected condition and gets a `404`. A crashed database connection is unexpected and gets a `500`.

---

## 13.4 Logging and Common Middleware

### Logging

**Logging** means recording what your application does, so you can understand what happened after the fact, especially in production, where you cannot attach a debugger.

**Log levels** classify the importance of each message:

| Level | Meaning | Example |
|---|---|---|
| **debug** | Detailed information for developers | "Cache miss for key user:7" |
| **info** | Normal, noteworthy events | "Server started on port 3000" |
| **warn** | Something unexpected but survivable | "Retrying failed request" |
| **error** | An operation failed | "Could not connect to database" |

Good logging practices:

- **Log requests**: method, path, status code, and duration, as in the example above.
- **Use structured logs** (such as JSON lines) in production so tools can search and filter them.
- **Include a request ID** so you can follow one request through many log lines and many services.
- **Never log secrets or personal data**, such as passwords, tokens, and full credit card numbers.
- Choose levels thoughtfully so important messages are not buried in noise.

### Common middleware you will meet

| Middleware | What it does |
|---|---|
| **Body parsing** | Turns the raw request body (JSON, form data) into usable data |
| **CORS** | Controls which other websites' scripts may call your API (see below) |
| **Compression** | Shrinks responses so they travel faster |
| **Static files** | Serves images, CSS, and JavaScript directly |
| **Sessions / cookies** | Remembers a user between requests |
| **Authentication** | Identifies the caller (Lesson 15) |
| **Rate limiting** | Restricts how many requests a client may make in a time window |
| **Security headers** | Adds protective HTTP headers to every response |
| **CSRF protection** | Defends against forged form submissions (Lesson 15) |
| **Request logging** | Records each request, as in the example |

In Express, many of these come from small packages. For example (shown for illustration):

```javascript
const helmet = require("helmet");
const cors = require("cors");
const rateLimit = require("express-rate-limit");

app.use(helmet());                                   // security headers
app.use(cors({ origin: "https://myapp.example" })); // allow one trusted site
app.use(rateLimit({ windowMs: 15 * 60 * 1000, limit: 100 })); // 100 requests per 15 min
```

### Understanding CORS

Browsers enforce the **same-origin policy**: a script loaded from one website is not allowed to read responses from a *different* origin (a different domain, protocol, or port) unless that server explicitly permits it. **CORS (Cross-Origin Resource Sharing)** is the mechanism for granting that permission, using response headers such as `Access-Control-Allow-Origin`.

This matters whenever your frontend (for example, `https://myapp.example`) calls an API on a different address (`https://api.myapp.example`). For certain requests, the browser first sends a "preflight" `OPTIONS` request to ask whether the real request is allowed. If you see a "blocked by CORS policy" error in the browser console, it means the *server's* CORS configuration needs to allow your frontend's origin.

Two points to remember: CORS is enforced by **browsers**, not by servers or tools such as `curl`, and it is **not** a substitute for authentication. Keep the allowed origins list as narrow as you can.

---

[Previous](./[12]-HTTP-And-REST-APIs.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[14]-Working-With-Databases.md)
