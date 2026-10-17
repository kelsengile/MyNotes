[Previous](./[5]-Routers-And-Project-Structure.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[7]-Validation-And-Error-Handling.md)

*Building APIs*

# Lesson 6 - Building REST APIs

REST is a set of conventions for designing web APIs around **resources** and standard HTTP methods. Following them makes your API predictable for anyone who uses it.

---

## 6.1 REST principles in Express

**Core ideas:**

1. **Resources are nouns.** URLs name things (`/users`, `/orders/5`), not actions (`/getUsers`, `/deleteOrder`).
2. **HTTP methods are the verbs.** The method says what to do with the resource.
3. **Stateless.** Each request carries everything the server needs (for example, an auth token). The server does not remember previous requests.
4. **Meaningful status codes.** `201` for created, `404` for missing, and so on.
5. **Consistent representation.** Usually JSON, with the same shape every time.

### Mapping methods to actions

| Method | URL | Action | Success status |
|---|---|---|---|
| `GET` | `/todos` | List all todos | `200` |
| `GET` | `/todos/:id` | Get one todo | `200` |
| `POST` | `/todos` | Create a todo | `201` |
| `PUT` | `/todos/:id` | Replace a todo entirely | `200` |
| `PATCH` | `/todos/:id` | Update some fields | `200` |
| `DELETE` | `/todos/:id` | Delete a todo | `204` |

### Good vs bad URL design

| Bad | Good |
|---|---|
| `GET /getAllTodos` | `GET /todos` |
| `POST /createTodo` | `POST /todos` |
| `POST /todos/5/delete` | `DELETE /todos/5` |
| `GET /todo` (singular) | `GET /todos` (plural collection) |

### Nested resources

Use nesting to express ownership, but keep it shallow (two levels at most):

```
GET /users/5/orders        → orders belonging to user 5
GET /orders/12             → one order directly
```

### Idempotency

An operation is **idempotent** if repeating it gives the same result.

- `GET`, `PUT`, `DELETE` are idempotent.
- `POST` is not (two posts create two resources).

---

## 6.2 CRUD endpoints

CRUD stands for Create, Read, Update, Delete. Here is a complete in-memory todo API (we will swap in a real database in Lesson 8).

```js
// routes/todos.js
import { Router } from 'express';

const router = Router();

let todos = [
  { id: 1, title: 'Learn Express', done: false },
  { id: 2, title: 'Build an API', done: false },
];
let nextId = 3;

// READ all
router.get('/', (req, res) => {
  res.json(todos);
});

// READ one
router.get('/:id', (req, res) => {
  const todo = todos.find((t) => t.id === Number(req.params.id));
  if (!todo) return res.status(404).json({ error: 'Todo not found' });
  res.json(todo);
});

// CREATE
router.post('/', (req, res) => {
  const { title } = req.body ?? {};
  if (!title || typeof title !== 'string') {
    return res.status(400).json({ error: 'title is required' });
  }

  const todo = { id: nextId++, title, done: false };
  todos.push(todo);

  res
    .status(201)
    .location(`/api/todos/${todo.id}`)
    .json(todo);
});

// UPDATE (replace)
router.put('/:id', (req, res) => {
  const index = todos.findIndex((t) => t.id === Number(req.params.id));
  if (index === -1) return res.status(404).json({ error: 'Todo not found' });

  const { title, done } = req.body ?? {};
  if (typeof title !== 'string' || typeof done !== 'boolean') {
    return res.status(400).json({ error: 'title (string) and done (boolean) are required' });
  }

  todos[index] = { id: todos[index].id, title, done };
  res.json(todos[index]);
});

// UPDATE (partial)
router.patch('/:id', (req, res) => {
  const todo = todos.find((t) => t.id === Number(req.params.id));
  if (!todo) return res.status(404).json({ error: 'Todo not found' });

  if (req.body?.title !== undefined) todo.title = req.body.title;
  if (req.body?.done !== undefined) todo.done = req.body.done;

  res.json(todo);
});

// DELETE
router.delete('/:id', (req, res) => {
  const index = todos.findIndex((t) => t.id === Number(req.params.id));
  if (index === -1) return res.status(404).json({ error: 'Todo not found' });

  todos.splice(index, 1);
  res.status(204).end();
});

export default router;
```

Mount it:

```js
app.use(express.json());
app.use('/api/todos', todosRouter);
```

Try it:

```bash
curl http://localhost:3000/api/todos
curl -X POST http://localhost:3000/api/todos -H "Content-Type: application/json" -d '{"title":"Write tests"}'
curl -X PATCH http://localhost:3000/api/todos/1 -H "Content-Type: application/json" -d '{"done":true}'
curl -X DELETE http://localhost:3000/api/todos/2
```

**PUT vs PATCH:** `PUT` replaces the whole resource, so all fields must be provided. `PATCH` changes only the fields you send.

---

## 6.3 Pagination and filtering

Returning thousands of records at once is slow and wasteful. Let clients request slices and narrow results with query parameters.

### Pagination with `page` and `limit`

```js
router.get('/', (req, res) => {
  const page = Math.max(1, Number(req.query.page) || 1);
  const limit = Math.min(100, Math.max(1, Number(req.query.limit) || 10));

  const start = (page - 1) * limit;
  const items = todos.slice(start, start + limit);

  res.json({
    data: items,
    meta: {
      page,
      limit,
      total: todos.length,
      totalPages: Math.ceil(todos.length / limit),
    },
  });
});
```

`GET /api/todos?page=2&limit=5` returns items 6 to 10.

**Always cap `limit`** (here at 100) so a client cannot request a million rows.

### Filtering

```js
// GET /api/todos?done=true&search=express
router.get('/', (req, res) => {
  let result = [...todos];

  if (req.query.done !== undefined) {
    const done = req.query.done === 'true';
    result = result.filter((t) => t.done === done);
  }

  if (req.query.search) {
    const term = String(req.query.search).toLowerCase();
    result = result.filter((t) => t.title.toLowerCase().includes(term));
  }

  res.json(result);
});
```

### Sorting

```js
// GET /api/todos?sort=title&order=desc
const allowedSort = ['id', 'title'];
const sortField = allowedSort.includes(req.query.sort) ? req.query.sort : 'id';
const direction = req.query.order === 'desc' ? -1 : 1;

result.sort((a, b) => (a[sortField] > b[sortField] ? 1 : -1) * direction);
```

Only allow sorting by fields you list. Never pass raw user input into a database sort or query.

### Putting it together

Apply in this order: **filter → sort → paginate**. Count the total **after filtering** but **before slicing**, so `total` reflects matching items.

### Offset vs cursor pagination

| | Offset (`page`/`limit`) | Cursor (`after=<id>`) |
|---|---|---|
| Simple to build | Yes | Slightly harder |
| Jump to page N | Yes | No |
| Stable when data changes | No (items can shift) | Yes |
| Fast on huge tables | Slower | Faster |

Start with offset; switch to cursor pagination for feeds and very large datasets.

---

## 6.4 API versioning

APIs change. Versioning lets you introduce breaking changes without breaking existing clients.

### Strategy 1: URL path (most common)

```js
import v1Routes from './routes/v1/index.js';
import v2Routes from './routes/v2/index.js';

app.use('/api/v1', v1Routes);
app.use('/api/v2', v2Routes);
```

```
src/routes/
├── v1/
│   ├── index.js
│   └── users.js
└── v2/
    ├── index.js
    └── users.js
```

Clients call `/api/v1/users` or `/api/v2/users`. It is visible, easy to test in a browser, and easy to cache.

### Strategy 2: Header versioning

```js
app.use('/api', (req, res, next) => {
  req.apiVersion = req.get('Accept-Version') ?? '1';
  next();
});
```

Cleaner URLs, but harder to test and less visible.

### Strategy 3: Query parameter

`GET /api/users?version=2`. Simple, but it can be messy with caching.

### When do you need a new version?

| Change | Breaking? |
|---|---|
| Add a new optional field to a response | No |
| Add a new endpoint | No |
| Rename or remove a field | **Yes** |
| Change a field's type | **Yes** |
| Change required inputs | **Yes** |

### Good practices

- Only bump the version for **breaking** changes.
- Keep old versions running for a deprecation period.
- Announce deprecation with headers:

```js
app.use('/api/v1', (req, res, next) => {
  res.set('Deprecation', 'true');
  res.set('Sunset', 'Wed, 31 Dec 2026 23:59:59 GMT');
  next();
});
```

- Share logic through services so v1 and v2 controllers do not duplicate business code.

---

[Previous](./[5]-Routers-And-Project-Structure.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[7]-Validation-And-Error-Handling.md)
