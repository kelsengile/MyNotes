[Previous](./[9]-Authentication.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[11]-Security-Best-Practices.md)

*Building APIs*

# Lesson 10 - Views, Static Files, And Uploads

Not every Express app is a JSON API. Express can also serve images and stylesheets, render HTML pages from templates, accept file uploads, and stream large responses. This lesson covers all four.

---

## 10.1 Serving static files

**Static files** are files sent exactly as they are on disk: images, CSS, client-side JavaScript, fonts, PDFs. The built-in `express.static` middleware serves a folder.

```
my-express-app/
├── public/
│   ├── css/style.css
│   ├── js/app.js
│   └── images/logo.png
└── src/
    └── app.js
```

```js
import express from 'express';
import path from 'node:path';

const app = express();

app.use(express.static(path.join(import.meta.dirname, '../public')));
```

Now `public/css/style.css` is available at `http://localhost:3000/css/style.css`. The folder name (`public`) is **not** part of the URL.

> **Always use an absolute path.** `express.static('public')` is relative to where you launched `node`, so it breaks if you start the app from another folder. `import.meta.dirname` (Node.js 20.11+) gives the folder of the current file in ES modules. On older versions, use `path.dirname(fileURLToPath(import.meta.url))`.

### A virtual path prefix

Mount the middleware on a path to add a URL prefix that does not exist on disk:

```js
app.use('/static', express.static(publicDir));
// public/css/style.css → /static/css/style.css
```

### Options worth knowing

```js
app.use(
  express.static(publicDir, {
    maxAge: '1d',        // Cache-Control max-age for browsers
    etag: true,          // lets browsers revalidate cheaply (default)
    index: 'index.html', // file served for a folder URL (default)
    dotfiles: 'ignore',  // never serve .env and other dotfiles (default)
  })
);
```

### Order and fallthrough

- Put `express.static` **before** your routes. If a file matches, the response is sent and the routes never run. If nothing matches, the request continues to the next middleware.
- You can register more than one static folder. Express tries them in order.
- Serving a single-page app (React, Vue)? Serve the build folder, then add a catch-all that returns `index.html` for client-side routes:

```js
app.use(express.static(distDir));

app.get('/{*splat}', (req, res) => {          // Express 5 wildcard syntax (see Lesson 2)
  res.sendFile(path.join(distDir, 'index.html'));
});
```

Put API routes (such as `/api`) **above** this catch-all so they are not swallowed.

> **Never** point `express.static` at your project root. You would expose source code, `package.json`, and secrets. Serve one dedicated folder only.

---

## 10.2 Template engines like EJS and Pug

A **template engine** builds HTML by mixing a template file with data. It is useful when the server should send finished pages instead of JSON (dashboards, emails, simple sites).

| Engine | Style | Example |
|---|---|---|
| **EJS** | HTML with embedded JavaScript | `<h1><%= title %></h1>` |
| **Pug** | Indentation-based, no angle brackets | `h1= title` |

### Setting up EJS

```bash
npm install ejs
```

```js
app.set('view engine', 'ejs');                              // default file extension
app.set('views', path.join(import.meta.dirname, 'views'));  // where templates live

app.get('/', (req, res) => {
  res.render('index', {
    title: 'My Blog',
    posts: [
      { id: 1, title: 'First post' },
      { id: 2, title: 'Second post' },
    ],
  });
});
```

`res.render('index', data)` finds `views/index.ejs`, fills it in, and sends the HTML.

```html
<!-- views/index.ejs -->
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel="stylesheet" href="/css/style.css" />
  </head>
  <body>
    <h1><%= title %></h1>

    <% if (posts.length === 0) { %>
      <p>No posts yet.</p>
    <% } else { %>
      <ul>
        <% posts.forEach((post) => { %>
          <li><a href="/posts/<%= post.id %>"><%= post.title %></a></li>
        <% }) %>
      </ul>
    <% } %>
  </body>
</html>
```

### EJS tags

| Tag | Meaning |
|---|---|
| `<%= value %>` | Output the value, **HTML-escaped** (safe) |
| `<%- value %>` | Output the value **raw** (dangerous with user data) |
| `<% code %>` | Run JavaScript without output (loops, conditions) |
| `<%# comment %>` | Comment, not sent to the browser |

> **Security:** Use `<%= %>` for anything a user could have typed. It escapes characters like `<` so injected `<script>` tags show up as harmless text. Use `<%- %>` only for HTML you fully trust.

### Partials (reusable pieces)

```html
<!-- views/partials/header.ejs -->
<header><nav><a href="/">Home</a> | <a href="/about">About</a></nav></header>
```

```html
<!-- in any page -->
<%- include('partials/header') %>
```

### Data available to every template

Values on `res.locals` (Lesson 4) and `app.locals` are visible in all templates:

```js
app.locals.siteName = 'My Blog';              // set once

app.use((req, res, next) => {
  res.locals.user = req.user ?? null;         // set per request
  next();
});
```

### Pug (the alternative)

```bash
npm install pug
```

```js
app.set('view engine', 'pug');
```

```pug
//- views/index.pug
doctype html
html
  head
    title= title
  body
    h1= title
    ul
      each post in posts
        li= post.title
```

Pug is shorter to write but has its own syntax to learn. Pick the one your team prefers. They work the same way from Express's point of view.

### Forms and the POST-redirect-GET pattern

After a form submission, redirect instead of rendering directly. This prevents duplicate submissions when the user refreshes:

```js
app.use(express.urlencoded({ extended: true }));

app.post('/posts', (req, res) => {
  // save req.body ...
  res.redirect('/');          // browser then makes a fresh GET
});
```

---

## 10.3 File uploads with Multer

HTML forms and clients send files as `multipart/form-data`. Neither `express.json()` nor `express.urlencoded()` can read it, so the standard tool is **Multer**.

```bash
npm install multer
```

### Saving to disk

```js
import multer from 'multer';
import path from 'node:path';
import crypto from 'node:crypto';

const storage = multer.diskStorage({
  destination: (req, file, cb) => cb(null, 'uploads/'),
  filename: (req, file, cb) => {
    const ext = path.extname(file.originalname).toLowerCase();
    cb(null, `${crypto.randomUUID()}${ext}`);   // random name: no clashes, no path tricks
  },
});

const upload = multer({
  storage,
  limits: { fileSize: 2 * 1024 * 1024 },        // 2 MB max
  fileFilter: (req, file, cb) => {
    const allowed = ['image/jpeg', 'image/png', 'image/webp'];
    cb(null, allowed.includes(file.mimetype));  // true = accept, false = skip
  },
});
```

Create the `uploads/` folder first and add it to `.gitignore`.

### Upload routes

```js
// One file in the field named "avatar"
app.post('/profile/avatar', upload.single('avatar'), (req, res) => {
  if (!req.file) return res.status(400).json({ error: 'No valid file uploaded' });

  res.status(201).json({
    filename: req.file.filename,
    size: req.file.size,
    mimetype: req.file.mimetype,
  });
});

// Several files in one field (max 5)
app.post('/gallery', upload.array('photos', 5), (req, res) => {
  res.status(201).json({ count: req.files.length });
});

// Text fields and files together
app.post('/posts', upload.single('cover'), (req, res) => {
  res.json({ title: req.body.title, cover: req.file?.filename });
});
```

The matching HTML form must use `enctype="multipart/form-data"` and the same field name:

```html
<form action="/profile/avatar" method="post" enctype="multipart/form-data">
  <input type="file" name="avatar" />
  <button>Upload</button>
</form>
```

| Multer method | Use it for |
|---|---|
| `upload.single('field')` | One file → `req.file` |
| `upload.array('field', max)` | Many files, one field → `req.files` |
| `upload.fields([{ name, maxCount }, ...])` | Different files in different fields |
| `upload.none()` | Multipart form with text only |

### Memory storage

`multer.memoryStorage()` keeps the file in a Buffer (`req.file.buffer`) instead of writing to disk. This suits files you will immediately forward to cloud storage such as S3, but always set a small `fileSize` limit so a big upload cannot exhaust memory.

### Handling upload errors

Multer reports problems as a `MulterError`. Translate them in your error handler (Lesson 7):

```js
import multer from 'multer';

export function errorHandler(err, req, res, next) {
  if (err instanceof multer.MulterError) {
    const message = err.code === 'LIMIT_FILE_SIZE' ? 'File too large' : err.message;
    return res.status(400).json({ error: message });
  }
  // ... other errors
  next(err);
}
```

### Upload security checklist

- Always set `limits.fileSize`.
- Never trust `file.mimetype` alone. It comes from the client. For sensitive cases, inspect the file's actual bytes with a library such as `file-type`.
- Never use `file.originalname` as the stored name. It can contain `../` or collide with other files.
- Store uploads **outside** the folder served by `express.static`, unless you intend them to be public. Serve private files through an authenticated route.
- In production, prefer object storage (S3 or similar) over the app server's disk, since disks are lost when containers restart.

---

## 10.4 Streaming responses

Calling `res.send` loads the whole body into memory first. For large files or long-running output, **streaming** sends data in chunks as it becomes available, so memory use stays small and the client starts receiving data immediately.

### Streaming a file

```js
import fs from 'node:fs';
import { pipeline } from 'node:stream/promises';

app.get('/video', async (req, res) => {
  res.set('Content-Type', 'video/mp4');
  const stream = fs.createReadStream('media/intro.mp4');

  try {
    await pipeline(stream, res);     // pipes data and cleans up on errors
  } catch (err) {
    if (!res.headersSent) throw err; // before sending began, let Express handle it
    // otherwise the connection was closed by the client, which is normal
  }
});
```

Use `pipeline` rather than `stream.pipe(res)`. It destroys both streams properly if either one fails or the client disconnects, preventing leaked file handles.

> For ordinary downloads, `res.sendFile` and `res.download` (Lesson 3) already stream, and they also support range requests for video seeking. Write manual streams only when you need custom behavior.

### Streaming generated data

`res.write` sends a chunk, and `res.end` finishes the response. This is handy for exports and progress output:

```js
app.get('/export.csv', async (req, res) => {
  res.set({
    'Content-Type': 'text/csv',
    'Content-Disposition': 'attachment; filename="users.csv"',
  });

  res.write('id,name\n');
  for (let id = 1; id <= 100000; id++) {
    const ok = res.write(`${id},User ${id}\n`);
    if (!ok) await new Promise((resolve) => res.once('drain', resolve));  // respect backpressure
  }
  res.end();
});
```

**Backpressure** means the client is receiving slower than you are sending. When `res.write` returns `false`, wait for the `drain` event before writing more. `pipeline` does this for you automatically.

### Streaming from a database

Many database drivers expose cursors or streams. Combined with `pipeline`, you can export millions of rows without loading them all:

```js
import { Readable } from 'node:stream';

app.get('/export.json', async (req, res) => {
  res.type('application/x-ndjson');   // one JSON object per line

  const rows = Readable.from(
    (async function* () {
      for await (const user of cursorOfUsers()) {   // your database cursor
        yield JSON.stringify(user) + '\n';
      }
    })()
  );

  await pipeline(rows, res);
});
```

### When to stream

| Situation | Stream? |
|---|---|
| Small JSON responses | No, `res.json` is fine |
| Large files or media | Yes |
| Big exports (CSV, reports) | Yes |
| Live updates to the browser | Yes, see Server-Sent Events in Lesson 14 |

---

[Previous](./[9]-Authentication.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[11]-Security-Best-Practices.md)
