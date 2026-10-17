[Previous](./[3]-Requests-And-Responses.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[5]-Routers-And-Project-Structure.md)

*Basics*

# Lesson 4 - Middleware

Middleware is the heart of Express. An Express app is essentially a chain of middleware functions, and route handlers are just the last link in that chain.

---

## 4.1 What middleware is

A middleware function has access to the request, the response, and a function called `next`:

```js
function myMiddleware(req, res, next) {
  // do something with req or res
  next();   // pass control to the next function in the chain
}
```

Each middleware can:

1. Run any code.
2. Change `req` or `res` (for example, attach a user to `req.user`).
3. End the request by sending a response.
4. Call `next()` to continue to the next middleware.

Think of it as an assembly line:

```
Request → [logger] → [body parser] → [auth check] → [route handler] → Response
```

### Registering middleware

```js
app.use(myMiddleware);                  // runs for every request
app.use('/api', myMiddleware);          // runs for paths starting with /api
app.get('/profile', myMiddleware, handler);   // runs only for this route
```

### Types of middleware

| Type | Registered with | Example |
|---|---|---|
| **Application-level** | `app.use()` | Logging every request |
| **Router-level** | `router.use()` | Auth for one group of routes |
| **Built-in** | `express.json()` etc. | Body parsing |
| **Third-party** | `npm install` | `cors`, `helmet`, `morgan` |
| **Error-handling** | `app.use((err, req, res, next) => ...)` | Covered in Lesson 7 |

---

## 4.2 Built-in middleware

Express ships with a few essential middleware functions.

| Middleware | Purpose |
|---|---|
| `express.json()` | Parses `application/json` bodies into `req.body` |
| `express.urlencoded()` | Parses HTML form bodies (`application/x-www-form-urlencoded`) |
| `express.text()` | Parses `text/plain` bodies into a string |
| `express.raw()` | Parses bodies into a Buffer (useful for webhooks) |
| `express.static()` | Serves files from a folder (see Lesson 10) |
| `express.Router()` | Creates modular route groups (see Lesson 5) |

```js
import express from 'express';

const app = express();

app.use(express.json({ limit: '1mb' }));            // reject bodies over 1 MB
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

app.post('/echo', (req, res) => {
  res.json({ you_sent: req.body });
});
```

Without `express.json()`, `req.body` is `undefined` in Express 5.

**Parsers only run when the `Content-Type` matches.** A request with `Content-Type: application/json` is handled by `express.json()` and ignored by `express.urlencoded()`.

---

## 4.3 Writing custom middleware

### Example 1: A request logger

```js
function logger(req, res, next) {
  const start = Date.now();

  res.on('finish', () => {
    const ms = Date.now() - start;
    console.log(`${req.method} ${req.originalUrl} ${res.statusCode} - ${ms}ms`);
  });

  next();
}

app.use(logger);
```

The `finish` event fires when the response has been sent, so you can measure the full duration.

### Example 2: Authentication check

```js
function requireApiKey(req, res, next) {
  const key = req.get('x-api-key');

  if (key !== process.env.API_KEY) {
    return res.status(401).json({ error: 'Invalid API key' });  // stop here
  }

  next();
}

app.use('/admin', requireApiKey);
```

Always use `return` when you send a response so the function does not continue running.

### Example 3: Attaching data for later handlers

```js
function attachRequestId(req, res, next) {
  req.id = crypto.randomUUID();
  res.set('X-Request-Id', req.id);
  next();
}

app.use(attachRequestId);

app.get('/', (req, res) => {
  res.json({ requestId: req.id });
});
```

For data meant for templates, use `res.locals` instead of adding properties to `req`:

```js
app.use((req, res, next) => {
  res.locals.currentYear = new Date().getFullYear();
  next();
});
```

### Example 4: A configurable middleware (factory)

Return a middleware from a function to make it reusable with options:

```js
function requireRole(role) {
  return (req, res, next) => {
    if (req.user?.role !== role) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}

app.delete('/users/:id', requireRole('admin'), (req, res) => {
  res.status(204).end();
});
```

### Passing errors with `next(err)`

Calling `next` with an argument signals an error and skips straight to error-handling middleware:

```js
app.get('/risky', (req, res, next) => {
  const error = new Error('Something broke');
  next(error);
});
```

(Lesson 7 shows how to handle these errors properly.)

---

## 4.4 Order of execution

Middleware runs **in the order you register it**. This is the single most important rule.

```js
app.use((req, res, next) => {
  console.log('1: first');
  next();
});

app.use((req, res, next) => {
  console.log('2: second');
  next();
});

app.get('/', (req, res) => {
  console.log('3: route handler');
  res.send('Done');
});

app.use((req, res, next) => {
  console.log('4: never runs for "/" because the route already responded');
  next();
});
```

A request to `/` prints `1`, `2`, `3`.

### Common ordering mistakes

**Mistake 1: Parsing the body after the route**

```js
app.post('/users', (req, res) => res.json(req.body));  // req.body is undefined
app.use(express.json());                                // too late
```

Register `express.json()` **before** the routes that need it.

**Mistake 2: Auth after the route it should protect**

```js
app.get('/secret', (req, res) => res.send('secret data'));
app.use(requireLogin);   // never protects /secret
```

### Recommended order

```js
// 1. Security and utility middleware
app.use(helmet());
app.use(cors());
app.use(logger);

// 2. Body parsers
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// 3. Static files
app.use(express.static('public'));

// 4. Routes
app.use('/api', apiRouter);

// 5. 404 handler
app.use((req, res) => res.status(404).json({ error: 'Not found' }));

// 6. Error handler (always last)
app.use((err, req, res, next) => {
  res.status(500).json({ error: 'Server error' });
});
```

### Route-level middleware order

Middleware listed in a single route runs left to right:

```js
app.post('/posts', authenticate, validatePost, createPost);
//                  1st          2nd           3rd
```

If `authenticate` sends a 401 and does not call `next()`, the other two never run.

---

[Previous](./[3]-Requests-And-Responses.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[5]-Routers-And-Project-Structure.md)
