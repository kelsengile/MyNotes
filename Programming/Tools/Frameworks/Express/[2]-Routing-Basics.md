[Previous](./[1]-What-Is-Express.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[3]-Requests-And-Responses.md)

*Basics*

# Lesson 2 - Routing Basics

Routing decides which function runs when a request arrives. A route combines an HTTP method (like `GET`), a path (like `/users`), and a handler function.

---

## 2.1 Route methods and paths

The general shape of a route is:

```js
app.METHOD(PATH, HANDLER);
```

Express has a method for every HTTP verb:

| Method | Typical purpose | Example |
|---|---|---|
| `app.get` | Read data | `GET /users` |
| `app.post` | Create data | `POST /users` |
| `app.put` | Replace data | `PUT /users/5` |
| `app.patch` | Partially update data | `PATCH /users/5` |
| `app.delete` | Remove data | `DELETE /users/5` |
| `app.all` | Match every method | Logging, global checks |

```js
app.get('/users', (req, res) => res.send('List users'));
app.post('/users', (req, res) => res.send('Create user'));
app.put('/users/:id', (req, res) => res.send('Replace user'));
app.delete('/users/:id', (req, res) => res.send('Delete user'));
```

**Chaining with `app.route()`:** When several methods share one path, group them to avoid repeating it.

```js
app.route('/books')
  .get((req, res) => res.send('List books'))
  .post((req, res) => res.send('Add a book'));
```

**Wildcards and special paths (Express 5 syntax):**

```js
// Matches /files/anything/here (named wildcard, required in Express 5)
app.get('/files/*filepath', (req, res) => {
  res.send(req.params.filepath);   // array of path segments
});

// Optional segment: matches /posts and /posts/2024
app.get('/posts{/:year}', (req, res) => {
  res.send(req.params.year ?? 'all years');
});

// Catch everything, including "/"
app.use((req, res) => res.status(404).send('Not found'));
```

> **Express 4 vs 5:** In Express 4 you could write `'/files/*'` or `'/users/:id?'`. Express 5 no longer supports those. Wildcards must be named (`*name`) and optional parts use braces (`{...}`).

---

## 2.2 Route parameters and query strings

### Route parameters

A parameter is a named placeholder in the path, written with a colon. Express fills it in and stores it in `req.params`.

```js
app.get('/users/:id', (req, res) => {
  res.json({ userId: req.params.id });
});
// GET /users/42  →  { "userId": "42" }
```

Multiple parameters work too:

```js
app.get('/users/:userId/posts/:postId', (req, res) => {
  const { userId, postId } = req.params;
  res.json({ userId, postId });
});
// GET /users/7/posts/19  →  { "userId": "7", "postId": "19" }
```

> **Important:** Parameters are always **strings**. Convert them yourself: `const id = Number(req.params.id);`

**Preprocessing a parameter with `app.param`:**

```js
app.param('id', (req, res, next, value) => {
  if (!/^\d+$/.test(value)) {
    return res.status(400).json({ error: 'id must be a number' });
  }
  req.userId = Number(value);
  next();
});

app.get('/users/:id', (req, res) => res.json({ id: req.userId }));
```

### Query strings

The part after `?` in a URL is the query string. Express parses it into `req.query`.

```js
app.get('/search', (req, res) => {
  const { q, page = '1' } = req.query;
  res.json({ q, page: Number(page) });
});
// GET /search?q=express&page=2  →  { "q": "express", "page": 2 }
```

| URL | `req.query` |
|---|---|
| `/search?q=node` | `{ q: 'node' }` |
| `/search?tag=a&tag=b` | `{ tag: ['a', 'b'] }` |
| `/search` | `{}` |

**Params vs query: when to use which?**

| Use route parameters for | Use query strings for |
|---|---|
| Identifying a specific resource (`/users/42`) | Filtering, sorting, paging (`/users?role=admin&sort=name`) |
| Required values | Optional values |

> **Express 5 note:** The default query parser is now `"simple"` (Node's `querystring`), so nested syntax like `?a[b]=1` is no longer turned into objects unless you opt in with `app.set('query parser', 'extended')`.

---

## 2.3 Route handlers

A handler is a function `(req, res, next)`. It must do one of two things: **end the request** (by sending a response) or **pass control on** with `next()`.

```js
app.get('/ping', (req, res) => {
  res.send('pong');          // ends the request
});
```

**Multiple handlers on one route.** You can pass several functions. Each calls `next()` to continue to the next one.

```js
const checkApiKey = (req, res, next) => {
  if (req.get('x-api-key') !== 'secret') {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
};

const logRequest = (req, res, next) => {
  console.log(`${req.method} ${req.originalUrl}`);
  next();
};

app.get('/secure', logRequest, checkApiKey, (req, res) => {
  res.send('You are in');
});
```

Handlers can also be passed as an array:

```js
app.get('/secure', [logRequest, checkApiKey], (req, res) => res.send('OK'));
```

**Skipping the rest of a route with `next('route')`:**

```js
app.get('/items/:id',
  (req, res, next) => {
    if (req.params.id === '0') return next('route'); // skip to the next matching route
    next();
  },
  (req, res) => res.send('Normal item')
);

app.get('/items/:id', (req, res) => res.send('Special handling for id 0'));
```

**Common mistake: forgetting to respond.** If a handler neither sends a response nor calls `next()`, the client waits until it times out.

```js
// Bad: request hangs forever
app.get('/broken', (req, res) => {
  console.log('Handled, but no response sent');
});
```

**Another common mistake: responding twice.** After a response is sent, do not send another. Use `return` to stop:

```js
app.get('/user/:id', (req, res) => {
  if (!req.params.id) {
    return res.status(400).send('Missing id');   // return stops the function
  }
  res.send('OK');
});
```

---

## 2.4 Route ordering

Express checks routes **from top to bottom** and uses the **first** one that matches and sends a response. Order therefore matters.

**Problem: a general route placed first swallows a specific one.**

```js
app.get('/users/:id', (req, res) => res.send('One user'));
app.get('/users/new', (req, res) => res.send('New user form'));  // never reached!
```

A request to `/users/new` matches `/users/:id` first (with `id = "new"`).

**Fix: put specific routes before dynamic ones.**

```js
app.get('/users/new', (req, res) => res.send('New user form'));
app.get('/users/:id', (req, res) => res.send('One user'));
```

**The 404 handler goes last.** Anything not matched by earlier routes falls through to it:

```js
app.get('/', (req, res) => res.send('Home'));
app.get('/about', (req, res) => res.send('About'));

// Must be after all other routes
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});
```

**Ordering rules of thumb:**

1. Global middleware (parsers, loggers) first.
2. Specific routes before parameterized routes.
3. Parameterized routes before wildcards.
4. 404 handler after all routes.
5. Error-handling middleware at the very end (see Lesson 7).

---

[Previous](./[1]-What-Is-Express.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[3]-Requests-And-Responses.md)
