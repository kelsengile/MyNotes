[Previous](./[9]-Buffers-And-Streams.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[11]-Child-Processes-And-Worker-Threads.md)

*Core APIs*

# Lesson 10 - HTTP And Networking

Node.js was designed for networking. This lesson shows how to build a web server using only built-in modules, make requests to other servers, and gives an overview of other network protocols.

## 10.1 The http module

HTTP is a request-response protocol. A client sends a **request** (method, URL, headers, optional body) and the server sends back a **response** (status code, headers, optional body).

Common methods: `GET` (read), `POST` (create), `PUT`/`PATCH` (update), `DELETE` (remove).

Common status codes:

| Code | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 301/302 | Redirect |
| 400 | Bad request |
| 401/403 | Unauthorized / Forbidden |
| 404 | Not found |
| 500 | Server error |

The `http` module provides `http.createServer()`, which takes a function called for every incoming request. That function receives a request object (a readable stream) and a response object (a writable stream).

---

## 10.2 Building a server from scratch

```js
const http = require("node:http");

const server = http.createServer((req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);

  if (req.method === "GET" && url.pathname === "/") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Welcome home");
  } else if (req.method === "GET" && url.pathname === "/api/time") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify({ time: new Date().toISOString() }));
  } else {
    res.writeHead(404, { "Content-Type": "text/plain" });
    res.end("Not found");
  }
});

server.listen(3000, () => {
  console.log("Server running at http://localhost:3000");
});
```

Reading a JSON request body (it arrives as a stream):

```js
function readBody(req) {
  return new Promise((resolve, reject) => {
    let data = "";
    req.on("data", (chunk) => (data += chunk));
    req.on("end", () => {
      try { resolve(JSON.parse(data)); } catch (e) { reject(e); }
    });
    req.on("error", reject);
  });
}
```

You must always end every response with `res.end()`, or the client waits forever. Writing routing, parsing, and error handling by hand gets repetitive, which is why frameworks like Express exist (see Lesson 20).

---

## 10.3 Making HTTP requests

Modern Node.js includes the web-standard `fetch` function globally.

```js
const res = await fetch("https://jsonplaceholder.typicode.com/todos/1");

if (!res.ok) throw new Error(`HTTP ${res.status}`);

const data = await res.json();
console.log(data.title);
```

Sending data:

```js
const res = await fetch("https://example.com/api/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Ana" }),
});
```

Notes:

- `fetch` only rejects on network failure; HTTP errors like 404 do **not** reject, so check `res.ok`.
- Set a timeout with `AbortSignal.timeout(5000)`: `fetch(url, { signal: AbortSignal.timeout(5000) })`.
- The lower-level `http.request()` and `https.request()` still exist for advanced needs.

---

## 10.4 HTTPS, TCP, and WebSockets overview

**HTTPS** is HTTP encrypted with TLS. Use `https.createServer` with a certificate and key. In production, it is common to let a reverse proxy (such as Nginx) or a hosting platform handle HTTPS.

```js
const https = require("node:https");
const fs = require("node:fs");

https.createServer(
  { key: fs.readFileSync("key.pem"), cert: fs.readFileSync("cert.pem") },
  (req, res) => res.end("Secure hello")
).listen(8443);
```

**TCP** is the lower-level protocol HTTP is built on. The `net` module creates raw TCP servers and clients, useful for custom protocols.

```js
const net = require("node:net");

const server = net.createServer((socket) => {
  socket.write("Hello client\n");
  socket.on("data", (d) => socket.write("Echo: " + d));
});
server.listen(4000);
```

**WebSockets** keep a connection open so the server and browser can send messages to each other at any time. They are used for chat, live dashboards, and games. Node.js has a WebSocket client built in, but servers usually use a library such as `ws` or Socket.IO.

```js
// npm install ws
const { WebSocketServer } = require("ws");

const wss = new WebSocketServer({ port: 8080 });
wss.on("connection", (socket) => {
  socket.on("message", (msg) => socket.send(`You said: ${msg}`));
});
```

[Previous](./[9]-Buffers-And-Streams.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[11]-Child-Processes-And-Worker-Threads.md)