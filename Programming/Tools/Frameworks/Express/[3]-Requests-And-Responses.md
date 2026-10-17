[Previous](./[2]-Routing-Basics.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[4]-Middleware.md)

*Basics*

# Lesson 3 - Requests And Responses

Every handler receives two objects: `req` (what the client sent) and `res` (what you send back). Mastering them is the core of working with Express.

---

## 3.1 The request object

`req` extends Node's `IncomingMessage` with many helpful properties.

| Property / method | What it contains | Example value |
|---|---|---|
| `req.method` | HTTP method | `'GET'` |
| `req.originalUrl` | Full URL path and query | `'/api/users?page=2'` |
| `req.path` | Path without the query | `'/users'` (relative to mount point) |
| `req.params` | Route parameters | `{ id: '5' }` |
| `req.query` | Parsed query string | `{ page: '2' }` |
| `req.body` | Parsed request body | `{ name: 'Ana' }` |
| `req.headers` | All headers (lowercase keys) | `{ 'user-agent': '...' }` |
| `req.get('Name')` | One header, case-insensitive | `req.get('Content-Type')` |
| `req.ip` | Client IP address | `'::1'` |
| `req.hostname` | Host from the `Host` header | `'localhost'` |
| `req.protocol` | `'http'` or `'https'` | `'http'` |
| `req.cookies` | Parsed cookies (needs `cookie-parser`) | `{ theme: 'dark' }` |

```js
app.get('/inspect', (req, res) => {
  res.json({
    method: req.method,
    url: req.originalUrl,
    userAgent: req.get('User-Agent'),
    query: req.query,
    ip: req.ip,
  });
});
```

### Reading the request body

The body is **not** parsed automatically. You must add body-parsing middleware first:

```js
app.use(express.json());                          // parses JSON bodies
app.use(express.urlencoded({ extended: true }));  // parses HTML form bodies

app.post('/users', (req, res) => {
  const { name, email } = req.body;
  res.status(201).json({ name, email });
});
```

Test it with curl:

```bash
curl -X POST http://localhost:3000/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Ana","email":"ana@example.com"}'
```

> **Express 5 note:** If no parser matched the request, `req.body` is `undefined` (Express 4 gave you `{}`). Always check, or guard with `req.body ?? {}`.

---

## 3.2 Sending JSON and status codes

### Sending responses

| Method | Use it to |
|---|---|
| `res.json(obj)` | Send JSON (sets `Content-Type: application/json`) |
| `res.send(data)` | Send text, HTML, a Buffer, or an object |
| `res.sendStatus(code)` | Send just a status code with its default text |
| `res.status(code)` | Set the status (chainable, does **not** send) |
| `res.end()` | End the response with no body |

```js
app.get('/api/user', (req, res) => {
  res.json({ id: 1, name: 'Ana' });
});

app.get('/html', (req, res) => {
  res.send('<h1>Hello</h1>');
});

app.delete('/api/user/1', (req, res) => {
  res.status(204).end();        // success, nothing to return
});

app.get('/teapot', (req, res) => {
  res.sendStatus(418);          // body: "I'm a Teapot"
});
```

**Chain `status()` before the send method:**

```js
res.status(201).json({ id: 99, name: 'New user' });
```

### Choosing status codes

| Code | Name | When to use it |
|---|---|---|
| `200` | OK | Successful `GET`, `PUT`, `PATCH` |
| `201` | Created | A resource was created (`POST`) |
| `204` | No Content | Success with no body (usually `DELETE`) |
| `301` / `308` | Moved Permanently | URL changed forever |
| `302` / `307` | Found / Temporary Redirect | URL changed temporarily |
| `304` | Not Modified | Client's cached copy is still valid |
| `400` | Bad Request | Client sent invalid data |
| `401` | Unauthorized | Not logged in / bad credentials |
| `403` | Forbidden | Logged in but not allowed |
| `404` | Not Found | Resource does not exist |
| `409` | Conflict | Duplicate or state conflict |
| `422` | Unprocessable Content | Well-formed but fails validation |
| `429` | Too Many Requests | Rate limit hit |
| `500` | Internal Server Error | Unexpected server bug |

> **Express 5 note:** The old `res.json(obj, status)` and `res.send(status)` signatures were removed. Always use `res.status(code).json(obj)`.

---

## 3.3 Redirects and headers

### Redirects

```js
app.get('/old-page', (req, res) => {
  res.redirect('/new-page');                  // 302 by default
});

app.get('/moved', (req, res) => {
  res.redirect(301, '/permanent-home');       // permanent redirect
});

app.get('/external', (req, res) => {
  res.redirect('https://expressjs.com');
});
```

> **Security warning:** Never redirect to a URL taken directly from user input (`res.redirect(req.query.next)`). Attackers can use it to send users to malicious sites. Validate against an allowlist or require relative paths.

### Headers

```js
app.get('/headers', (req, res) => {
  res.set('X-Powered-By-Me', 'Express');
  res.set({
    'Cache-Control': 'no-store',
    'X-Request-Id': '12345',
  });
  res.type('json');           // sets Content-Type: application/json
  res.send('{"ok":true}');
});
```

Reading and removing:

```js
req.get('Authorization');            // read a request header
res.get('Content-Type');             // read a header you already set
res.removeHeader('X-Powered-By');    // remove one
```

Many developers hide the default `X-Powered-By: Express` header:

```js
app.disable('x-powered-by');
```

### Sending files

```js
import path from 'node:path';

app.get('/report', (req, res) => {
  res.sendFile(path.resolve('files/report.pdf'));       // displays in browser
});

app.get('/download', (req, res) => {
  res.download(path.resolve('files/report.pdf'), 'my-report.pdf');  // forces download
});
```

`sendFile` requires an **absolute** path (or a `root` option).

### Content negotiation

```js
app.get('/data', (req, res) => {
  res.format({
    'text/plain': () => res.send('hello'),
    'application/json': () => res.json({ message: 'hello' }),
    default: () => res.status(406).send('Not Acceptable'),
  });
});
```

---

## 3.4 Cookies

Cookies are small pieces of data the browser stores and sends back with each request.

### Setting a cookie

```js
app.get('/set-theme', (req, res) => {
  res.cookie('theme', 'dark', {
    maxAge: 1000 * 60 * 60 * 24,  // 1 day in milliseconds
    httpOnly: true,               // JavaScript in the page cannot read it
    secure: true,                 // only sent over HTTPS
    sameSite: 'lax',              // limits cross-site sending
  });
  res.send('Theme saved');
});
```

| Option | Purpose |
|---|---|
| `maxAge` / `expires` | How long the cookie lives |
| `httpOnly` | Blocks `document.cookie` access (protects against XSS theft) |
| `secure` | HTTPS only |
| `sameSite` | `'strict'`, `'lax'`, or `'none'`; helps prevent CSRF |
| `domain` / `path` | Scope of where the cookie is sent |
| `signed` | Adds a signature so tampering is detected |

### Reading cookies

Install the parser:

```bash
npm install cookie-parser
```

```js
import cookieParser from 'cookie-parser';

app.use(cookieParser('my-signing-secret'));

app.get('/read-theme', (req, res) => {
  res.json({ theme: req.cookies.theme ?? 'light' });
});
```

### Signed cookies

```js
res.cookie('userId', '42', { signed: true });

// Later:
req.signedCookies.userId;   // '42', or false if it was tampered with
```

### Deleting a cookie

```js
app.get('/reset', (req, res) => {
  res.clearCookie('theme');   // options like path must match those used when setting
  res.send('Cleared');
});
```

> **Remember:** Signed cookies are tamper-evident, not secret. Anyone can still read the value. Never store passwords or sensitive data in a cookie.

---

[Previous](./[2]-Routing-Basics.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[4]-Middleware.md)
