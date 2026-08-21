[Previous](./[23]-Working-with-Databases.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[25]-Authentication-and-Sessions.md)

*Back-End Basics*

# Lesson 24 - Real-Time Communication with WebSockets

## 24.1 The Limits of Request/Response

HTTP (Lesson 1) is built around the client asking and the server answering — the server can never initiate a message on its own. For features like chat apps, live notifications, or collaborative editing, where the server needs to push updates the moment they happen, plain HTTP requires inefficient workarounds like **polling** (repeatedly asking "anything new?" on a timer).

---

## 24.2 What WebSockets Provide

A **WebSocket** is a persistent, two-way connection between client and server. After an initial HTTP "handshake," the connection upgrades and stays open, letting either side send messages to the other at any time without the overhead of starting a new HTTP request each time.

```js
// Client
const socket = new WebSocket("wss://example.com/chat");

socket.addEventListener("open", () => {
  socket.send("Hello server!");
});

socket.addEventListener("message", (event) => {
  console.log("Received:", event.data);
});
```

---

## 24.3 A Minimal WebSocket Server

Using the popular `ws` library in Node.js:

```js
const { WebSocketServer } = require("ws");
const wss = new WebSocketServer({ port: 8080 });

wss.on("connection", (socket) => {
  socket.on("message", (data) => {
    // broadcast to every connected client
    wss.clients.forEach(client => client.send(data.toString()));
  });
});
```

---

## 24.4 When to Use WebSockets vs Polling

WebSockets shine when updates are frequent and low-latency matters (chat, live dashboards, multiplayer features). For infrequent updates, simple polling or **Server-Sent Events (SSE)** — a lighter one-way (server-to-client only) streaming alternative — are often simpler to implement and operate, and don't require managing persistent bidirectional connections at scale.

---

## 24.5 Real-World Considerations

Persistent connections have costs plain HTTP doesn't: servers must track open connections, handle reconnects when a client's network drops, and often need dedicated infrastructure to scale beyond a single server instance (since a message might need to reach a client connected to a different server). Libraries like **Socket.IO** wrap raw WebSockets with automatic reconnection, fallbacks, and room/broadcast helpers, trading a bit of overhead for a much easier implementation.

---

[Previous](./[23]-Working-with-Databases.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[25]-Authentication-and-Sessions.md)
