[Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[2]-Routing-Basics.md)

*Basics*

# Lesson 1 - What Is Express

Express is a small framework that sits on top of Node.js and makes it much easier to build web servers and APIs. This lesson explains what problem Express solves, how it compares to alternatives, and gets your first server running.

---

## 1.1 Where Express fits on Node.js

Node.js ships with a built-in `http` module that can already create a server. The problem is that it is very low-level: you parse URLs, read request bodies, and set headers by hand.

**Plain Node.js:**

```js
import http from 'node:http';

const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/hello') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ message: 'Hello' }));
  } else {
    res.writeHead(404);
    res.end('Not found');
  }
});

server.listen(3000);
```

**The same thing with Express:**

```js
import express from 'express';

const app = express();

app.get('/hello', (req, res) => {
  res.json({ message: 'Hello' });
});

app.listen(3000);
```

Express does not replace Node. It wraps the same `http` server and adds:

| Feature | What it gives you |
|---|---|
| **Routing** | Match a method and path (`GET /users/:id`) to a function |
| **Middleware** | A pipeline of functions that run before your final handler |
| **Request helpers** | `req.params`, `req.query`, `req.body`, `req.get()` |
| **Response helpers** | `res.json()`, `res.status()`, `res.redirect()`, `res.sendFile()` |

The layers look like this:

```
Your application code
        ↓
     Express
        ↓
  Node.js `http` module
        ↓
   Operating system / network
```

> **Which version?** This Topic uses **Express 5**, which is the current `latest` release on npm. Where Express 4 behaves differently, a note points it out. Express 5 requires Node.js 18 or newer.

---

## 1.2 Express vs Fastify and Koa

Express is not the only Node.js framework. Here is how it compares to two popular alternatives:

| | **Express** | **Fastify** | **Koa** |
|---|---|---|---|
| Philosophy | Minimal, unopinionated | Fast, schema-driven | Tiny core, modern async |
| Built-in features | Routing, basic middleware | Routing, validation, serialization, logging | Almost nothing (add packages) |
| Middleware style | `(req, res, next)` | Hooks and plugins | `async (ctx, next)` |
| Ecosystem size | Largest | Large and growing | Smaller |
| Learning curve | Very gentle | Gentle | Gentle |
| Best for | Learning, general APIs, huge package choice | High-throughput APIs | Developers who want a blank slate |

**Why still learn Express?**

- Most Node.js tutorials, job listings, and legacy projects use it.
- Its middleware concept is the same idea used by almost every backend framework.
- Once you know Express, Fastify and Koa feel familiar within a day.

---

## 1.3 Installing and your first server

**Step 1: Make sure Node.js is installed.**

```bash
node --version   # should print v18 or higher (v20+ recommended)
```

**Step 2: Create a project.**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**Step 3: Enable ES modules.** Open `package.json` and add `"type": "module"` so you can use `import` syntax:

```json
{
  "name": "my-express-app",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node server.js",
    "dev": "node --watch server.js"
  },
  "dependencies": {
    "express": "^5.0.0"
  }
}
```

**Step 4: Create `server.js`.**

```js
import express from 'express';

const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Hello from Express!');
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
```

**Step 5: Run it.**

```bash
npm run dev
```

Visit `http://localhost:3000` in your browser and you will see the message. The `--watch` flag restarts the server whenever you save a file, so you do not need `nodemon`.

**What each line does:**

| Code | Meaning |
|---|---|
| `express()` | Creates an application object (`app`) |
| `app.get('/', handler)` | When a `GET` request arrives at `/`, run `handler` |
| `res.send(...)` | Sends a response and ends the request |
| `app.listen(PORT, cb)` | Starts listening for connections on that port |

> **CommonJS alternative:** If you prefer `require`, remove `"type": "module"` and write `const express = require('express');`. Everything else stays the same.

---

## 1.4 Project layout

Express does not force a folder structure. For a tiny app, one file is fine. As it grows, a layout like this keeps things organized (the details are covered in Lesson 5):

```
my-express-app/
├── src/
│   ├── app.js            # creates and configures the Express app
│   ├── server.js         # starts listening (imports app.js)
│   ├── config/           # environment and settings
│   ├── routes/           # URL definitions
│   ├── controllers/      # request/response logic
│   ├── services/         # business logic
│   ├── middleware/       # custom middleware
│   └── models/           # database models
├── tests/
├── .env                  # secrets (never commit this)
├── .gitignore
└── package.json
```

**Two files you should always create right away:**

`.gitignore`

```
node_modules/
.env
```

`.env` (example)

```
PORT=3000
NODE_ENV=development
```

**Why split `app.js` and `server.js`?** `app.js` builds the app and exports it. `server.js` imports it and calls `listen`. This lets your tests import the app without opening a real network port (see Lesson 13).

```js
// src/app.js
import express from 'express';

const app = express();

app.get('/', (req, res) => res.send('Hello'));

export default app;
```

```js
// src/server.js
import app from './app.js';

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Listening on ${PORT}`));
```

---

[Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[2]-Routing-Basics.md)
