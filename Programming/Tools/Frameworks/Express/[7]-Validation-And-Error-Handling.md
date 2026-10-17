[Previous](./[6]-Building-REST-APIs.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[8]-Working-With-Databases.md)

*Building APIs*

# Lesson 7 - Validation And Error Handling

Never trust data from a client. This lesson shows how to check input, and how to turn every failure into a clean, consistent error response.

---

## 7.1 Validating input with Zod or Joi

Manual checks like `if (!title)` get messy fast. Schema libraries let you describe valid data once and reuse it.

### Zod

```bash
npm install zod
```

```js
import { z } from 'zod';

const createUserSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
  age: z.number().int().min(13).optional(),
});
```

Use `safeParse`, which returns a result instead of throwing:

```js
const result = createUserSchema.safeParse({ name: 'A', email: 'nope' });

if (!result.success) {
  console.log(result.error.issues);
  // [{ path: ['name'], message: '...' }, { path: ['email'], message: '...' }]
} else {
  console.log(result.data);   // validated, typed data
}
```

### A reusable validation middleware

```js
// middleware/validate.js
export const validate = (schema, source = 'body') => (req, res, next) => {
  const result = schema.safeParse(req[source]);

  if (!result.success) {
    return res.status(422).json({
      error: 'Validation failed',
      details: result.error.issues.map((issue) => ({
        field: issue.path.join('.'),
        message: issue.message,
      })),
    });
  }

  req[source] = result.data;   // replace with cleaned data
  next();
};
```

> **Express 5 note:** In Express 5, `req.query` is a read-only getter. Do not overwrite it. For query validation, store the result elsewhere, for example `req.validatedQuery = result.data`.

Using it on a route:

```js
import { validate } from '../middleware/validate.js';

router.post('/', validate(createUserSchema), (req, res) => {
  // req.body is guaranteed valid here
  res.status(201).json(req.body);
});
```

### Validating params and queries (with coercion)

Query and param values are strings, so use `z.coerce`:

```js
const listQuerySchema = z.object({
  page: z.coerce.number().int().min(1).default(1),
  limit: z.coerce.number().int().min(1).max(100).default(10),
});

const idParamSchema = z.object({
  id: z.coerce.number().int().positive(),
});
```

### Joi (an alternative)

```bash
npm install joi
```

```js
import Joi from 'joi';

const schema = Joi.object({
  name: Joi.string().min(2).required(),
  email: Joi.string().email().required(),
});

const { error, value } = schema.validate(req.body, { abortEarly: false });
if (error) {
  return res.status(422).json({ details: error.details.map((d) => d.message) });
}
```

| | Zod | Joi |
|---|---|---|
| TypeScript inference | Excellent | Limited |
| Style | Chained, functional | Chained, object-based |
| Best for | TypeScript projects | Long-established JavaScript projects |

Pick one and use it consistently.

---

## 7.2 Error-handling middleware

Express recognizes an error handler by its **four parameters**: `(err, req, res, next)`. You must declare all four, even if you do not use `next`.

```js
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ error: 'Internal Server Error' });
});
```

### How errors reach it

- Calling `next(err)` in any middleware or handler.
- Throwing an error inside a synchronous handler.
- A rejected promise or thrown error in an `async` handler (**automatic in Express 5**; see 7.4).

```js
app.get('/sync-crash', (req, res) => {
  throw new Error('Boom');        // caught by Express
});

app.get('/manual', (req, res, next) => {
  next(new Error('Passed along'));
});
```

### Placement

The error handler must come **after** all routes and other middleware:

```js
app.use('/api', routes);

app.use(notFoundHandler);     // 404
app.use(errorHandler);        // errors (last)
```

### A realistic 404 + error setup

```js
// middleware/errorHandlers.js
export function notFoundHandler(req, res, next) {
  res.status(404).json({ error: `Cannot ${req.method} ${req.originalUrl}` });
}

export function errorHandler(err, req, res, next) {
  // If a response has already started, delegate to Express's default handler
  if (res.headersSent) return next(err);

  const status = err.statusCode ?? 500;
  const isServerError = status >= 500;

  if (isServerError) console.error(err);

  res.status(status).json({
    error: isServerError && process.env.NODE_ENV === 'production'
      ? 'Internal Server Error'
      : err.message,
    ...(process.env.NODE_ENV !== 'production' && { stack: err.stack }),
  });
}
```

**Never leak stack traces or internal messages in production.** They help attackers.

---

## 7.3 Custom error classes

Custom classes let you throw meaningful errors from anywhere (services, controllers) and have one handler translate them into HTTP responses.

```js
// errors/AppError.js
export class AppError extends Error {
  constructor(message, statusCode = 500, details = undefined) {
    super(message);
    this.name = this.constructor.name;
    this.statusCode = statusCode;
    this.details = details;
    Error.captureStackTrace?.(this, this.constructor);
  }
}

export class BadRequestError extends AppError {
  constructor(message = 'Bad request', details) {
    super(message, 400, details);
  }
}

export class UnauthorizedError extends AppError {
  constructor(message = 'Unauthorized') {
    super(message, 401);
  }
}

export class ForbiddenError extends AppError {
  constructor(message = 'Forbidden') {
    super(message, 403);
  }
}

export class NotFoundError extends AppError {
  constructor(message = 'Resource not found') {
    super(message, 404);
  }
}

export class ConflictError extends AppError {
  constructor(message = 'Conflict') {
    super(message, 409);
  }
}
```

### Throwing from a service

```js
// services/userService.js
import { NotFoundError, ConflictError } from '../errors/AppError.js';

export function getUser(id) {
  const user = users.find((u) => u.id === id);
  if (!user) throw new NotFoundError(`User ${id} not found`);
  return user;
}

export function createUser(data) {
  if (users.some((u) => u.email === data.email)) {
    throw new ConflictError('Email already registered');
  }
  // ...
}
```

### The controller stays simple

```js
router.get('/:id', (req, res) => {
  res.json(getUser(Number(req.params.id)));   // errors bubble up automatically
});
```

### Improved central handler

```js
import { AppError } from '../errors/AppError.js';

export function errorHandler(err, req, res, next) {
  if (res.headersSent) return next(err);

  // Known, expected errors
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      error: err.message,
      ...(err.details && { details: err.details }),
    });
  }

  // Invalid JSON body from express.json()
  if (err.type === 'entity.parse.failed') {
    return res.status(400).json({ error: 'Malformed JSON' });
  }

  // Unknown errors: log and hide details
  console.error(err);
  res.status(500).json({ error: 'Internal Server Error' });
}
```

**Distinguish operational errors from bugs.** Operational errors (not found, invalid input) are expected and safe to show. Bugs (`TypeError`, database crashes) should be logged and shown only as a generic 500.

---

## 7.4 Handling async errors

### Express 5 (this Topic): automatic

If an `async` handler throws or returns a rejected promise, Express 5 forwards the error to your error handler for you.

```js
router.get('/:id', async (req, res) => {
  const user = await db.findUser(req.params.id);   // if this rejects, error handler runs
  if (!user) throw new NotFoundError();
  res.json(user);
});
```

No try/catch needed.

### Express 4: you must catch it yourself

In Express 4, an unhandled rejection in an async handler **hangs the request** or crashes the process. Two solutions:

**Option 1: try/catch with `next`**

```js
router.get('/:id', async (req, res, next) => {
  try {
    const user = await db.findUser(req.params.id);
    res.json(user);
  } catch (err) {
    next(err);
  }
});
```

**Option 2: a wrapper function**

```js
export const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

router.get('/:id', asyncHandler(async (req, res) => {
  res.json(await db.findUser(req.params.id));
}));
```

(Writing the wrapper is harmless in Express 5 too, in case you are maintaining mixed code.)

### Errors outside request handlers

Errors that happen outside a request (startup code, timers, background jobs) do not reach Express. Add process-level safety nets:

```js
process.on('unhandledRejection', (reason) => {
  console.error('Unhandled rejection:', reason);
  process.exit(1);          // let a process manager (PM2, Docker) restart the app
});

process.on('uncaughtException', (err) => {
  console.error('Uncaught exception:', err);
  process.exit(1);
});
```

After an uncaught exception the app may be in an unknown state, so the safest move is to log and exit, relying on a process manager to restart it (see Lesson 15).

### Summary checklist

- Validate all input at the edge with a schema.
- Throw custom errors from services and controllers.
- Register one 404 handler and one error handler, both last.
- Never expose stack traces in production.
- Log unexpected errors; return generic messages to clients.

---

[Previous](./[6]-Building-REST-APIs.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[8]-Working-With-Databases.md)
