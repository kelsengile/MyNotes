[Previous](./[11]-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[13]-Testing-Express-Applications.md)

*Hardening And Delivery*

# Lesson 12 - Logging And Debugging

When something breaks in production you cannot attach your editor to it. Logs are your eyes. This lesson shows how to record every request, write structured application logs, debug problems while developing, and watch the health of a running app.

---

## 12.1 Request logging with Morgan

**Morgan** is middleware that writes one line per HTTP request: method, URL, status code, and response time. (Lesson 4 built a tiny version by hand.)

```bash
npm install morgan
```

```js
import morgan from 'morgan';

app.use(morgan('dev'));
```

Output:

```
GET /api/todos 200 4.123 ms - 182
POST /api/todos 201 6.870 ms - 64
GET /api/todos/99 404 1.204 ms - 36
```

### Predefined formats

| Format | Output | Use it for |
|---|---|---|
| `dev` | Short, colored by status | Local development |
| `tiny` | Minimal line | Quick checks |
| `common` | Apache common log format | Basic production logs |
| `combined` | Apache combined format (adds referrer and user agent) | Production, analytics tools |

### Different logging per environment

```js
app.use(morgan(process.env.NODE_ENV === 'production' ? 'combined' : 'dev'));
```

### Custom formats and tokens

```js
morgan.token('id', (req) => req.id);   // uses the request id from Lesson 4

app.use(morgan(':id :method :url :status :response-time ms'));
```

### Skipping noise

```js
app.use(
  morgan('combined', {
    skip: (req, res) => req.path === '/health' || res.statusCode < 400,   // log only errors
  })
);
```

### Sending Morgan output to a file or logger

By default Morgan writes to the console. Pass a `stream` to send it elsewhere:

```js
import fs from 'node:fs';
import path from 'node:path';

const accessLog = fs.createWriteStream(path.join(import.meta.dirname, '../access.log'), { flags: 'a' });

app.use(morgan('combined', { stream: accessLog }));
```

> **Placement:** Register Morgan **early**, before your routes, so every request is recorded. Log files grow forever, so rotate them (the `rotating-file-stream` package, or your hosting platform's log handling) and in containers prefer writing to the console and letting the platform collect logs.

---

## 12.2 Application logging with Winston or Pino

Morgan only covers HTTP requests. For everything else (a payment failed, the database reconnected, a job finished) you need a proper **logger**. `console.log` has no levels, no structure, and no way to route output.

### Log levels

Levels let you control how much is recorded. Each level includes the ones above it:

| Level | Meaning | Example |
|---|---|---|
| `error` | Something failed | Database query threw |
| `warn` | Something suspicious | Retrying a request |
| `info` | Normal important events | Server started, user registered |
| `debug` | Details for developers | Query parameters |

Run production at `info`, and raise to `debug` temporarily when investigating.

### Structured logging

Instead of plain sentences, write logs as **JSON objects** so tools can search and filter them:

```json
{"level":"error","time":"2026-03-01T10:15:00.000Z","userId":42,"msg":"Payment failed","orderId":9001}
```

Searching for `userId = 42` across millions of lines is easy with JSON and almost impossible with free-form text.

### Pino

**Pino** is very fast and outputs JSON by default.

```bash
npm install pino pino-http
npm install --save-dev pino-pretty
```

```js
// src/logger.js
import pino from 'pino';

export const logger = pino({
  level: process.env.LOG_LEVEL ?? 'info',
  redact: ['req.headers.authorization', 'req.headers.cookie', 'password'],   // hide secrets
  ...(process.env.NODE_ENV !== 'production' && {
    transport: { target: 'pino-pretty' },     // human-readable in development
  }),
});
```

```js
logger.info('Server started');
logger.info({ userId: 42 }, 'User logged in');          // data first, message second
logger.error({ err, orderId: 9001 }, 'Payment failed'); // pass the error under "err"
```

Use `pino-http` to log every request in the same structured style:

```js
import pinoHttp from 'pino-http';
import { logger } from './logger.js';

app.use(pinoHttp({ logger }));

app.get('/', (req, res) => {
  req.log.info('Handling home page');   // each log line carries the request id automatically
  res.send('Hello');
});
```

### Winston

**Winston** is a popular, flexible logger built around **transports** (destinations).

```bash
npm install winston
```

```js
// src/logger.js
import winston from 'winston';

export const logger = winston.createLogger({
  level: process.env.LOG_LEVEL ?? 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),   // include stack traces for Error objects
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
  ],
});
```

```js
logger.info('Server started', { port: 3000 });
logger.error('Payment failed', { orderId: 9001, error: err.message });
```

### Pino vs Winston

| | Pino | Winston |
|---|---|---|
| Speed | Faster | Slower |
| Default output | JSON | Configurable |
| Approach | Minimal core, tools around it | Many built-in transports |
| Good choice for | New projects, high traffic | Projects needing many destinations |

### Using the logger in the error handler

Replace `console.error` from Lesson 7 so unexpected errors are recorded with context:

```js
export function errorHandler(err, req, res, next) {
  if (res.headersSent) return next(err);

  if (err instanceof AppError) {
    return res.status(err.statusCode).json({ error: err.message });
  }

  logger.error({ err, method: req.method, url: req.originalUrl }, 'Unhandled error');
  res.status(500).json({ error: 'Internal Server Error' });
}
```

### What to log and what to never log

| Log | Never log |
|---|---|
| Startup, shutdown, configuration (without secrets) | Passwords and password hashes |
| Errors with stack traces | Tokens, API keys, session ids |
| Important business events | Full credit card numbers and other sensitive personal data |
| Request id so you can trace one request across lines | Entire request bodies by default |

Use the logger's redaction feature (as in the Pino example) as a safety net, but do not rely on it alone.

---

## 12.3 Debugging techniques

### Read the error first

Most bugs announce themselves. Read the whole error message and the **stack trace**. The first line that points into your own code (not into `node_modules`) is usually where to look.

### Quick inspection with `console.log` and `console.dir`

```js
console.log('params:', req.params);
console.dir(req.body, { depth: null });   // print nested objects fully
console.table(users);                     // arrays of objects as a table
```

It is fast and fine for quick checks. Remove it afterwards, or replace it with `logger.debug`.

### The `debug` package and Express's own logs

Express and many libraries use the `debug` package. Turn its output on with an environment variable:

```bash
DEBUG=express:* node src/server.js        # everything Express does internally
DEBUG=express:router node src/server.js   # only routing decisions
```

On Windows PowerShell: `$env:DEBUG="express:*"; node src/server.js`.

Use it in your own code, too:

```bash
npm install debug
```

```js
import createDebug from 'debug';

const debug = createDebug('myapp:users');

debug('loading user %d', id);   // only prints when DEBUG=myapp:* is set
```

### The Node.js inspector (real breakpoints)

Pause your running code, step through lines, and inspect variables.

```bash
node --inspect-brk src/server.js
```

Then either open `chrome://inspect` in Chrome and click your process, or use your editor:

**VS Code:** open the Run and Debug panel, choose "Node.js", or create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Express",
      "program": "${workspaceFolder}/src/server.js",
      "envFile": "${workspaceFolder}/.env"
    }
  ]
}
```

Click in the gutter to set a **breakpoint**. When a request reaches that line the program pauses. Key controls:

| Control | Action |
|---|---|
| Continue | Run until the next breakpoint |
| Step over | Run the current line, do not enter functions |
| Step into | Go inside the function being called |
| Step out | Finish the current function |
| Watch / Variables | See values of `req`, `res`, and your own variables |

### Debugging HTTP itself

Many problems are not in the code but in the request. Use a client that shows exactly what is sent and received:

```bash
curl -i -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title":"Test"}'
```

The `-i` flag prints response headers. Tools like Postman, Insomnia, and Bruno do the same with a visual interface. Your browser's Network tab shows requests made by your front end.

### Common problems and where to look

| Symptom | Likely cause |
|---|---|
| Request hangs forever | A handler never sent a response or called `next()` (Lesson 2) |
| `req.body` is `undefined` | Missing `express.json()`, registered after the route, or missing `Content-Type` header (Lessons 3 and 4) |
| `Cannot set headers after they are sent` | Responding twice. Add `return` before early responses |
| A route is never reached | An earlier route or middleware matches first (Lesson 2) |
| `CORS` error in the browser console | Missing or misconfigured `cors` middleware (Lesson 11) |
| Works locally, fails in production | Different environment variables, Node.js version, or a missing dependency |
| Route works in curl but not the browser | CORS or cookies (`secure`, `sameSite`) |

### Debugging method

1. **Reproduce** the problem reliably.
2. **Narrow it down.** Comment out or bisect middleware until the problem disappears.
3. **Form one hypothesis** and test it, rather than changing many things at once.
4. **Fix and add a test** (Lesson 13) so it cannot return silently.

---

## 12.4 Monitoring

Logging tells you what happened. **Monitoring** tells you what is happening **right now** and alerts you when something is wrong.

### Health check endpoints

A load balancer, Docker, or Kubernetes needs a cheap way to ask "is this app OK?".

```js
// Liveness: is the process running?
app.get('/health', (req, res) => {
  res.json({ status: 'ok', uptime: process.uptime() });
});

// Readiness: can it serve traffic? (checks dependencies)
app.get('/ready', async (req, res) => {
  try {
    await prisma.$queryRaw`SELECT 1`;          // or mongoose.connection.db.admin().ping()
    res.json({ status: 'ready' });
  } catch {
    res.status(503).json({ status: 'unavailable' });
  }
});
```

| Endpoint | Question | Failure means |
|---|---|---|
| `/health` (liveness) | Is the process alive? | Restart it |
| `/ready` (readiness) | Can it handle requests right now? | Stop sending traffic to it, but do not restart |

Exclude these routes from rate limiting and noisy request logs.

### Metrics with Prometheus

Metrics are numbers over time: requests per second, error rate, response time, memory use. The `prom-client` package exposes them in a format that Prometheus (and Grafana for charts) reads.

```bash
npm install prom-client
```

```js
import client from 'prom-client';

client.collectDefaultMetrics();   // CPU, memory, event loop lag, and more

const httpDuration = new client.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Request duration in seconds',
  labelNames: ['method', 'route', 'status'],
});

app.use((req, res, next) => {
  const end = httpDuration.startTimer();
  res.on('finish', () => {
    end({ method: req.method, route: req.route?.path ?? 'unmatched', status: res.statusCode });
  });
  next();
});

app.get('/metrics', async (req, res) => {
  res.set('Content-Type', client.register.contentType);
  res.send(await client.register.metrics());
});
```

Use `req.route?.path` (such as `/users/:id`) as a label instead of the real URL. Real URLs contain ids, which would create endless distinct label values. Protect `/metrics` so it is not public.

### Error tracking

Services like **Sentry** capture unhandled errors with stack traces, request details, and the user affected, group duplicates together, and notify you. Install the SDK early in your app startup and follow its Express setup guide, since the exact integration steps change between versions.

### What to watch

| Metric | Why it matters |
|---|---|
| Error rate (5xx responses) | The clearest sign of trouble |
| Response time (p50, p95, p99) | Slow requests frustrate users even when nothing "fails" |
| Request rate | Spot traffic spikes and drops |
| Memory and CPU use | A steadily rising memory line suggests a leak |
| Event loop lag | High lag means something is blocking Node.js |
| Database connection pool use | Exhausted pools cause timeouts |

### Alerts

Collecting data is not enough. Set alerts on the important ones, for example "error rate above 5% for 5 minutes". Alert on symptoms users feel, and avoid so many alerts that people start ignoring them.

### Uptime monitoring

External services (UptimeRobot, Better Stack, and similar) ping your public URL from outside and alert you if it stops answering, which catches outages that internal monitoring cannot, such as a dead server.

---

[Previous](./[11]-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[13]-Testing-Express-Applications.md)
