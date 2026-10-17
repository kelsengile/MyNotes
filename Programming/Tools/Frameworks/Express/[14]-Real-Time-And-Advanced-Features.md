[Previous](./[13]-Testing-Express-Applications.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[15]-Deployment.md)

*Hardening And Delivery*

# Lesson 14 - Real-Time And Advanced Features

Normal HTTP is request and response: the client asks, the server answers, and the conversation ends. Some features need the server to talk first, such as chat messages, live notifications, or progress bars. Others need speed or work that should not block a request, such as caching and background jobs. This lesson covers WebSockets, Server-Sent Events, Redis caching, and job queues.

---

## 14.1 WebSockets with Socket.IO

A **WebSocket** is a long-lived, two-way connection between browser and server. Once open, either side can send messages at any time with very little overhead. **Socket.IO** is a library built on WebSockets that adds automatic reconnection, fallbacks, **rooms**, and named events.

### Setup

Socket.IO needs access to the underlying Node.js HTTP server, so you create that server yourself and pass it to both Express and Socket.IO:

```bash
npm install socket.io
```

```js
// src/server.js
import http from 'node:http';
import { Server } from 'socket.io';
import app from './app.js';

const httpServer = http.createServer(app);

const io = new Server(httpServer, {
  cors: { origin: ['http://localhost:5173'] },   // allowed front-end origins (Lesson 11)
});

io.on('connection', (socket) => {
  console.log(`Client connected: ${socket.id}`);

  socket.on('disconnect', (reason) => {
    console.log(`Client left: ${socket.id} (${reason})`);
  });
});

httpServer.listen(3000, () => console.log('Listening on 3000'));
```

Call `httpServer.listen`, **not** `app.listen`. `app.listen` would create a second server that Socket.IO is not attached to.

### Events

Socket.IO communicates through **named events** with any JSON-friendly data:

```js
io.on('connection', (socket) => {
  // Receive an event from this client
  socket.on('chat:message', (text) => {
    const message = { id: crypto.randomUUID(), text, from: socket.id, at: Date.now() };

    io.emit('chat:message', message);          // send to EVERY connected client
  });
});
```

| Method | Who receives it |
|---|---|
| `socket.emit(event, data)` | Only the client that owns this socket |
| `io.emit(event, data)` | Every connected client |
| `socket.broadcast.emit(event, data)` | Everyone except this client |
| `io.to(room).emit(event, data)` | Everyone in a room |

### The browser side

```html
<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io();                           // connects to the same server

  socket.on('chat:message', (message) => {
    console.log('New message:', message.text);
  });

  function send(text) {
    socket.emit('chat:message', text);
  }
</script>
```

When the front end runs on a different origin (such as Vite), install `socket.io-client` and call `io('http://localhost:3000')`.

### Rooms

A **room** is a named group of sockets, handy for chat channels or per-user notifications.

```js
io.on('connection', (socket) => {
  socket.on('room:join', (roomName) => {
    socket.join(roomName);
    io.to(roomName).emit('room:notice', `${socket.id} joined ${roomName}`);
  });

  socket.on('room:message', ({ room, text }) => {
    io.to(room).emit('room:message', { text, from: socket.id });
  });
});
```

Sockets leave rooms automatically when they disconnect.

### Acknowledgements

The sender can receive a confirmation, similar to a response:

```js
// Server
socket.on('chat:message', (text, callback) => {
  // ...save the message
  callback({ ok: true });
});

// Client
socket.emit('chat:message', 'Hello', (response) => {
  console.log(response.ok);
});
```

### Authenticating sockets

Anyone can open a WebSocket, so check identity during the handshake. Socket.IO middleware runs once per connection, similar to Express middleware:

```js
import jwt from 'jsonwebtoken';

io.use((socket, next) => {
  const token = socket.handshake.auth.token;      // client: io({ auth: { token } })

  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET);
    socket.data.userId = Number(payload.sub);     // store the identity on the socket
    next();
  } catch {
    next(new Error('Unauthorized'));              // connection is refused
  }
});

io.on('connection', (socket) => {
  socket.join(`user:${socket.data.userId}`);      // a private room per user
});
```

### Sending from regular Express routes

Make `io` available to routes so ordinary HTTP requests can trigger real-time messages:

```js
app.set('io', io);

app.post('/api/orders', async (req, res) => {
  const order = await createOrder(req.body);

  req.app.get('io').to(`user:${order.userId}`).emit('order:created', order);
  res.status(201).json(order);
});
```

### Important cautions

- **Validate all incoming socket data** as you do for HTTP (Lesson 7). Socket events are input from clients too.
- **Do not trust the client** about who it is. Use the identity set during the handshake.
- **Scaling:** with several app instances, an emit on one server will not reach sockets connected to another. Use the Socket.IO Redis adapter (`@socket.io/redis-adapter`) so instances share messages. Most platforms also need **sticky sessions** (a client always reaches the same instance) for the default polling transport.
- **Choose WebSockets for two-way features** (chat, collaborative editing, games). For one-way updates from server to browser, Server-Sent Events (next section) are simpler.

---

## 14.2 Server-sent events

**Server-Sent Events (SSE)** let the server push a stream of messages to the browser over a normal HTTP response that stays open. Communication is **one-way** (server to client), which is all you need for notifications, live feeds, progress bars, and dashboards.

| | **SSE** | **WebSockets** |
|---|---|---|
| Direction | Server → client | Both ways |
| Protocol | Plain HTTP | Separate protocol (upgraded from HTTP) |
| Reconnection | Built into the browser | Built into Socket.IO, manual otherwise |
| Extra library | None | Usually Socket.IO |
| Works with proxies and HTTP/2 | Easily | Needs extra configuration |

### A basic SSE endpoint

```js
app.get('/events', (req, res) => {
  res.set({
    'Content-Type': 'text/event-stream',     // tells the browser this is an event stream
    'Cache-Control': 'no-cache',
    Connection: 'keep-alive',
  });
  res.flushHeaders();                         // send the headers immediately

  let count = 0;
  const timer = setInterval(() => {
    count += 1;
    res.write(`data: ${JSON.stringify({ count })}\n\n`);   // a message ends with a blank line
  }, 1000);

  req.on('close', () => {                     // the client left: stop the work
    clearInterval(timer);
  });
});
```

### The message format

Each message is plain text lines ending with **two newlines**:

```
event: progress
id: 7
data: {"percent": 70}

```

| Field | Purpose |
|---|---|
| `data:` | The message payload (required). Multiple `data:` lines are joined |
| `event:` | An optional name so the client can listen for specific types |
| `id:` | An optional id. After a reconnect the browser sends it back as `Last-Event-ID` so you can resume |
| `retry:` | Milliseconds the browser should wait before reconnecting |
| `: text` | A comment line, often used as a keep-alive |

### The browser side

The browser's built-in `EventSource` handles connecting and **automatically reconnects** if the connection drops.

```js
const source = new EventSource('/events');

source.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Count:', data.count);
};

source.addEventListener('progress', (event) => {      // listens for "event: progress"
  console.log(JSON.parse(event.data).percent);
});

source.onerror = () => console.log('Connection lost, retrying...');

// source.close();   // stop listening
```

### Broadcasting to many clients

Keep a set of open responses and write to each when something happens:

```js
const clients = new Set();

app.get('/notifications', (req, res) => {
  res.set({
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    Connection: 'keep-alive',
  });
  res.flushHeaders();

  clients.add(res);
  req.on('close', () => clients.delete(res));    // always clean up
});

export function broadcast(event, data) {
  for (const res of clients) {
    res.write(`event: ${event}\ndata: ${JSON.stringify(data)}\n\n`);
  }
}

app.post('/announce', (req, res) => {
  broadcast('announcement', { text: req.body.text });
  res.status(202).end();
});
```

### Keep-alive and proxies

Idle connections can be closed by proxies. Send a comment line every 15 to 30 seconds:

```js
const keepAlive = setInterval(() => res.write(': ping\n\n'), 20000);
req.on('close', () => clearInterval(keepAlive));
```

Reverse proxies such as Nginx may buffer responses and delay events. Disable buffering for SSE routes (for Nginx, send the header `X-Accel-Buffering: no`).

### Cautions

- Browsers limit simultaneous HTTP/1.1 connections per site (around 6), and every open `EventSource` uses one. HTTP/2 removes this limit in practice.
- `EventSource` cannot set custom headers. For authentication use cookies, or pass a short-lived token in the query string.
- Do **not** use `compression` middleware on SSE routes without flushing, or messages will be held back.
- Always remove closed connections, or the server leaks memory.

---

## 14.3 Caching with Redis

A **cache** keeps the result of slow or repeated work so the next request is fast. **Redis** is an in-memory key-value store, very fast and shared by every app instance, which makes it the usual choice for caching in Node.js.

### Setup

```bash
docker run -d --name redis -p 6379:6379 redis:7
npm install redis
```

```js
// src/db/redis.js
import { createClient } from 'redis';

export const redis = createClient({ url: process.env.REDIS_URL ?? 'redis://localhost:6379' });

redis.on('error', (err) => console.error('Redis error', err));

await redis.connect();     // top-level await works in ES modules
```

### Basic commands

```js
await redis.set('greeting', 'hello');
await redis.get('greeting');                    // 'hello'

await redis.set('session:42', 'data', { EX: 60 });   // expires after 60 seconds
await redis.del('greeting');
await redis.incr('visits');                     // atomic counter
await redis.ttl('session:42');                  // seconds left
```

Redis stores strings. Convert objects with `JSON.stringify` and `JSON.parse`.

### The cache-aside pattern

The most common approach: **check the cache first, and on a miss load from the database and store the result.**

```js
// src/services/productService.js
import { redis } from '../db/redis.js';
import * as productRepo from '../repositories/productRepository.js';

const TTL_SECONDS = 60;

export async function getProduct(id) {
  const key = `product:${id}`;

  const cached = await redis.get(key);
  if (cached) return JSON.parse(cached);           // cache hit

  const product = await productRepo.findById(id);  // cache miss: hit the database
  if (product) {
    await redis.set(key, JSON.stringify(product), { EX: TTL_SECONDS });
  }
  return product;
}
```

### Invalidation

When data changes, remove the stale copy so users do not see old values:

```js
export async function updateProduct(id, data) {
  const product = await productRepo.update(id, data);
  await redis.del(`product:${id}`);      // next read repopulates the cache
  return product;
}
```

Keep a short **TTL** (time to live) on everything as a safety net, so mistakes in invalidation fix themselves.

### A reusable cache middleware

Cache whole GET responses for a short time:

```js
export function cacheResponse(seconds) {
  return async (req, res, next) => {
    if (req.method !== 'GET') return next();

    const key = `http:${req.originalUrl}`;
    const cached = await redis.get(key);

    if (cached) {
      res.set('X-Cache', 'HIT');
      return res.json(JSON.parse(cached));
    }

    const originalJson = res.json.bind(res);
    res.json = (body) => {
      if (res.statusCode === 200) {
        redis.set(key, JSON.stringify(body), { EX: seconds }).catch(() => {});
      }
      res.set('X-Cache', 'MISS');
      return originalJson(body);
    };

    next();
  };
}

app.get('/api/products', cacheResponse(30), listProducts);
```

> **Never cache user-specific responses under a shared key.** If `/api/me` is cached by URL alone, everyone receives the first user's data. Include the user id in the key, or do not cache it.

### Cache failure should not break the app

A cache is an optimization. If Redis is down, fall back to the database instead of returning errors:

```js
async function safeGet(key) {
  try {
    return await redis.get(key);
  } catch {
    return null;       // treat as a miss
  }
}
```

### Other uses of Redis

| Use | How |
|---|---|
| **Session store** | `connect-redis` with `express-session` (Lesson 9) |
| **Rate limit store** | `rate-limit-redis` (Lesson 11) |
| **Socket.IO adapter** | Share events across servers (14.1) |
| **Job queues** | BullMQ (14.4) |
| **Pub/Sub** | `redis.publish` and `subscribe` for messages between services |

### Caching guidance

| Cache | Do not cache |
|---|---|
| Data that is read often and changes rarely | Data that must always be exactly current (account balances) |
| Expensive queries and external API calls | Private data under shared keys |
| Results of heavy computation | Everything blindly. Measure first |

Also remember HTTP caching in the browser: headers such as `Cache-Control` and ETags (Lessons 3 and 10) reduce traffic before it even reaches your server.

---

## 14.4 Background jobs

Some work is too slow to do while the user waits: sending email, resizing images, generating reports, calling slow external services. If the handler does it directly, the request hangs and may time out. Instead, **put the work in a queue and respond immediately**. A separate **worker** processes it.

```
Client → POST /signup → Express → add job to queue → respond 202 right away
                                        ↓
                                 Worker takes job → sends welcome email
```

### BullMQ

**BullMQ** is a popular job queue for Node.js built on Redis. It provides retries, delays, scheduling, and concurrency control.

```bash
npm install bullmq
```

BullMQ needs its own Redis connection with `maxRetriesPerRequest: null`, using `ioredis`:

```bash
npm install ioredis
```

```js
// src/queues/connection.js
import IORedis from 'ioredis';

export const connection = new IORedis(process.env.REDIS_URL ?? 'redis://localhost:6379', {
  maxRetriesPerRequest: null,
});
```

### Adding jobs (in your Express app)

```js
// src/queues/emailQueue.js
import { Queue } from 'bullmq';
import { connection } from './connection.js';

export const emailQueue = new Queue('email', {
  connection,
  defaultJobOptions: {
    attempts: 3,                                   // retry up to 3 times
    backoff: { type: 'exponential', delay: 5000 }, // wait 5s, 10s, 20s
    removeOnComplete: 1000,                        // keep the last 1000 finished jobs
    removeOnFail: 5000,
  },
});
```

```js
app.post('/api/signup', async (req, res) => {
  const user = await registerUser(req.body);

  await emailQueue.add('welcome', { userId: user.id, email: user.email });

  res.status(201).json(user);       // the user does not wait for the email
});
```

### Processing jobs (the worker)

The worker is a **separate file and process**, so heavy work does not slow your web server:

```js
// src/worker.js
import { Worker } from 'bullmq';
import { connection } from './queues/connection.js';
import { sendEmail } from './services/emailService.js';

const worker = new Worker(
  'email',
  async (job) => {
    if (job.name === 'welcome') {
      await sendEmail({ to: job.data.email, subject: 'Welcome!' });
    }
  },
  { connection, concurrency: 5 }       // process up to 5 jobs at once
);

worker.on('completed', (job) => console.log(`Job ${job.id} done`));
worker.on('failed', (job, err) => console.error(`Job ${job?.id} failed:`, err.message));
```

Run both processes:

```bash
node src/server.js     # web server
node src/worker.js     # worker
```

If a job's function throws, BullMQ retries it according to `attempts` and `backoff`.

### Delayed and repeating jobs

```js
// Run once, 10 minutes from now
await emailQueue.add('reminder', { userId: 5 }, { delay: 10 * 60 * 1000 });

// Run every day at 08:00 (cron syntax)
await emailQueue.upsertJobScheduler(
  'daily-digest',
  { pattern: '0 8 * * *' },
  { name: 'digest', data: {} }
);
```

### Making jobs safe

| Principle | Why |
|---|---|
| **Idempotent jobs** (running twice gives the same result) | Retries and crashes mean a job may run more than once |
| **Small payloads** (send ids, not whole objects) | Data in the queue can become stale; load fresh data in the worker |
| **Set `attempts` and `backoff`** | Temporary failures such as network errors fix themselves |
| **Handle permanently failed jobs** | Alert someone or move them to a dead-letter review list |
| **Shut down workers gracefully** | `await worker.close()` on `SIGTERM` lets the current job finish |

### Do you need a queue?

| Situation | Use |
|---|---|
| Work takes under a few hundred ms | Just do it in the request |
| Slow work the user does not need to wait for | A queue and worker |
| Work that must survive restarts and be retried | A queue and worker |
| Simple recurring task on a single server | A cron job, or `node-cron` |

**Never** rely on `setTimeout` or fire-and-forget promises for important work. They vanish if the process restarts and fail silently.

### Showing job progress to users

Return `202 Accepted` with a job id, then let the client check on it, or push the result with Socket.IO or SSE from sections 14.1 and 14.2:

```js
app.post('/api/reports', async (req, res) => {
  const job = await reportQueue.add('generate', req.body);
  res.status(202).json({ jobId: job.id, statusUrl: `/api/reports/${job.id}` });
});

app.get('/api/reports/:id', async (req, res) => {
  const job = await reportQueue.getJob(req.params.id);
  if (!job) return res.status(404).json({ error: 'Job not found' });

  res.json({ state: await job.getState(), result: job.returnvalue ?? null });
});
```

---

[Previous](./[13]-Testing-Express-Applications.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[15]-Deployment.md)
