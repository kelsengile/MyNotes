[Previous](./[4]-Middleware.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[6]-Building-REST-APIs.md)

*Basics*

# Lesson 5 - Routers And Project Structure

Putting every route in one file works for a demo, but real applications need structure. Routers let you split an app into small, focused pieces.

---

## 5.1 express.Router

A **router** is a mini Express app that only handles routing and middleware. You create one, define routes on it, then mount it on a path in the main app.

```js
// routes/users.js
import { Router } from 'express';

const router = Router();

router.get('/', (req, res) => {
  res.json([{ id: 1, name: 'Ana' }]);
});

router.get('/:id', (req, res) => {
  res.json({ id: req.params.id });
});

router.post('/', (req, res) => {
  res.status(201).json(req.body);
});

export default router;
```

```js
// app.js
import express from 'express';
import usersRouter from './routes/users.js';

const app = express();
app.use(express.json());

app.use('/users', usersRouter);   // mount at /users

export default app;
```

Because of the mount path, the routes become:

| Route in router | Final URL |
|---|---|
| `router.get('/')` | `GET /users` |
| `router.get('/:id')` | `GET /users/:id` |
| `router.post('/')` | `POST /users` |

### Router-level middleware

Middleware added to a router only affects that router:

```js
const router = Router();

router.use((req, res, next) => {
  console.log('Users router hit');
  next();
});
```

### Nested routers and `mergeParams`

By default, a child router cannot see parameters from its parent's mount path. Use `mergeParams: true` to share them:

```js
// routes/posts.js  (mounted at /users/:userId/posts)
const router = Router({ mergeParams: true });

router.get('/', (req, res) => {
  res.json({ postsForUser: req.params.userId });
});

export default router;
```

```js
// routes/users.js
import postsRouter from './posts.js';

router.use('/:userId/posts', postsRouter);
// GET /users/5/posts  →  { "postsForUser": "5" }
```

---

## 5.2 Splitting routes into files

A good pattern is one router file per resource, plus a single `index.js` that collects them.

```
src/
├── app.js
├── server.js
└── routes/
    ├── index.js
    ├── users.js
    ├── posts.js
    └── auth.js
```

```js
// routes/index.js
import { Router } from 'express';
import usersRouter from './users.js';
import postsRouter from './posts.js';
import authRouter from './auth.js';

const router = Router();

router.use('/users', usersRouter);
router.use('/posts', postsRouter);
router.use('/auth', authRouter);

export default router;
```

```js
// app.js
import express from 'express';
import routes from './routes/index.js';

const app = express();

app.use(express.json());
app.use('/api', routes);          // everything lives under /api

app.use((req, res) => res.status(404).json({ error: 'Not found' }));

export default app;
```

Result: `GET /api/users`, `GET /api/posts`, `POST /api/auth/login`, and so on.

**Benefits:**

- Each file is short and easy to read.
- Teammates can work on different resources without conflicts.
- You can add a new resource by creating one file and one line in `index.js`.

> **ES module reminder:** When importing your own files, always include the `.js` extension (`'./users.js'`, not `'./users'`).

---

## 5.3 Controllers and services

As routes grow, handlers become long and mix concerns. A common solution is three layers:

```
Route       →  decides which URL maps to which function
Controller  →  handles req/res (reads input, sends output)
Service     →  contains business logic (knows nothing about HTTP)
```

### Why separate them?

- **Testable:** services can be tested without simulating HTTP requests.
- **Reusable:** the same service can be called from a route, a cron job, or a script.
- **Readable:** each file has one job.

### Example

**Service** (pure logic, no `req` or `res`):

```js
// services/userService.js
const users = [
  { id: 1, name: 'Ana' },
  { id: 2, name: 'Ben' },
];

export function getAllUsers() {
  return users;
}

export function getUserById(id) {
  return users.find((u) => u.id === id) ?? null;
}

export function createUser({ name }) {
  const user = { id: users.length + 1, name };
  users.push(user);
  return user;
}
```

**Controller** (translates between HTTP and the service):

```js
// controllers/userController.js
import * as userService from '../services/userService.js';

export function listUsers(req, res) {
  res.json(userService.getAllUsers());
}

export function getUser(req, res) {
  const user = userService.getUserById(Number(req.params.id));
  if (!user) return res.status(404).json({ error: 'User not found' });
  res.json(user);
}

export function addUser(req, res) {
  const user = userService.createUser(req.body);
  res.status(201).json(user);
}
```

**Route** (just wiring):

```js
// routes/users.js
import { Router } from 'express';
import * as controller from '../controllers/userController.js';

const router = Router();

router.get('/', controller.listUsers);
router.get('/:id', controller.getUser);
router.post('/', controller.addUser);

export default router;
```

### Rule of thumb

| If the code mentions... | It belongs in the... |
|---|---|
| `req`, `res`, status codes | Controller |
| Business rules, calculations, database calls | Service |
| URL paths and middleware lists | Route |

---

## 5.4 Configuration modules

Settings such as the port, database URL, and secrets should not be hard-coded. Store them in environment variables and read them in **one** central module.

### Loading environment variables

Node.js 20.6+ can load a `.env` file with no extra package:

```bash
node --env-file=.env src/server.js
```

Or use the popular `dotenv` package (works on every version):

```bash
npm install dotenv
```

```js
import 'dotenv/config';   // loads .env into process.env
```

### A central config module

```js
// config/index.js
import 'dotenv/config';

function required(name) {
  const value = process.env[name];
  if (!value) {
    throw new Error(`Missing required environment variable: ${name}`);
  }
  return value;
}

const config = {
  env: process.env.NODE_ENV ?? 'development',
  port: Number(process.env.PORT ?? 3000),
  databaseUrl: required('DATABASE_URL'),
  jwtSecret: required('JWT_SECRET'),
  isProduction: process.env.NODE_ENV === 'production',
};

export default config;
```

Use it anywhere:

```js
import config from './config/index.js';

app.listen(config.port, () => {
  console.log(`Running in ${config.env} on port ${config.port}`);
});
```

### Why this is better than reading `process.env` everywhere

- The app **fails fast** at startup if something is missing, instead of crashing later.
- Values are converted to the right type once (`Number(...)`).
- There is one place to see every setting the app needs.

### `.env` and `.env.example`

Never commit `.env`. Instead commit an example file with fake values so teammates know what to set:

```
# .env.example
PORT=3000
NODE_ENV=development
DATABASE_URL=postgres://user:password@localhost:5432/mydb
JWT_SECRET=change-me
```

---

[Previous](./[4]-Middleware.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[6]-Building-REST-APIs.md)
