[Previous](./[20]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[22]-Building-a-REST-API.md)

*Back-End Basics*

# Lesson 21 - Introduction to Servers & Node.js

## 21.1 What a Server Actually Is

A **server**, in the software sense, is just a program that listens on a network port for incoming requests and sends back responses (Lesson 1). It doesn't have to be special hardware — any computer running server software (even your own laptop, during development) can act as one.

---

## 21.2 Node.js

**Node.js** is a JavaScript runtime that runs outside the browser, letting developers write server-side code in the same language used on the front end. It's built on Chrome's V8 engine and adds APIs browsers don't have (file system access, networking, process control) while removing browser-only APIs (like `document`).

```js
// server.js
const http = require("http");

const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello from Node!");
});

server.listen(3000, () => console.log("Server running on port 3000"));
```

---

## 21.3 Ports

A **port** is a numbered endpoint on a machine that a specific process listens on, letting multiple services run on the same computer without colliding (a web server on port 3000, a database on port 5432). `localhost:3000` means "port 3000 on this same machine."

---

## 21.4 Express: A Minimal Web Framework

Writing raw `http` server code for every route quickly gets repetitive. **Express** is the most widely used Node.js web framework, providing routing, middleware, and request/response helpers on top of Node's built-in HTTP module:

```js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello from Express!");
});

app.listen(3000, () => console.log("Listening on port 3000"));
```

---

## 21.5 Middleware

**Middleware** are functions that run between a request arriving and a response being sent — used for logging, parsing request bodies, authentication checks, and more. Each middleware can inspect or modify the request/response, then pass control to the next one via `next()`:

```js
app.use(express.json()); // parse JSON request bodies

app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next(); // hand off to the next middleware/route
});
```

---

[Previous](./[20]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[22]-Building-a-REST-API.md)
