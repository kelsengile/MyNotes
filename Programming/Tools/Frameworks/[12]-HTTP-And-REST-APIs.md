[Previous](./[11]-Styling-And-User-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[13]-Middleware-And-Request-Handling.md)

*Building The Backend*

# Lesson 12 - HTTP And REST APIs

Now we move from what users see to what happens behind the scenes. Nearly every backend framework exists to receive **HTTP requests** and produce **HTTP responses**, and most modern backends expose their functionality as **REST APIs** that frontends and mobile apps consume. This lesson explains the web request cycle, the vocabulary of HTTP, and how to design clean API endpoints.

## 12.1 How the Web Request Cycle Works

When you type an address into a browser, or when your app asks a server for data, a **request-response cycle** takes place:

```text
1. Client builds a request ──────────────────────────────┐
                                                          ▼
2. The network delivers it to the server        ┌──────────────────┐
   (DNS finds the address, a secure             │      Server      │
    connection is opened)                       │ ┌──────────────┐ │
                                                │ │  Framework   │ │
3. The framework receives the request,          │ │ - routing    │ │
   matches a route, and runs middleware         │ │ - middleware │ │
   and your handler code                        │ │ - your code  │ │
                                                │ └──────┬───────┘ │
4. Your handler may read or write a database ◄──┼────────┘         │
                                                └────────┬─────────┘
5. The server sends back a response  ◄───────────────────┘
6. The client displays or uses the response
```

The steps in plain words:

1. The **client** (a browser, a phone app, or another program) sends a request.
2. **DNS** translates a name like `example.com` into a server address, and a connection is opened (normally encrypted with HTTPS).
3. The server's **framework** receives the request, finds the matching route (Lesson 10), passes it through **middleware** (Lesson 13), and runs your handler.
4. The handler often talks to a **database** (Lesson 14).
5. The handler produces a **response**, which the framework sends back.
6. The client uses the response: a browser renders HTML, and a frontend app reads JSON data.

### Anatomy of a request

An HTTP request is just structured text. A request to fetch a task looks like this:

```text
GET /tasks/1 HTTP/1.1
Host: api.example.com
Accept: application/json
Authorization: Bearer <token>
```

| Part | Purpose |
|---|---|
| **Method** (`GET`) | The action the client wants to perform |
| **Path** (`/tasks/1`) | Which resource it targets |
| **Headers** | Extra information: what formats the client accepts, credentials, and more |
| **Body** (optional) | Data sent to the server, as with creating something |

### Anatomy of a response

```text
HTTP/1.1 200 OK
Content-Type: application/json

{"id": 1, "title": "Learn REST", "done": false}
```

| Part | Purpose |
|---|---|
| **Status code** (`200 OK`) | Whether the request succeeded, and how |
| **Headers** | Information about the response, such as its format (`Content-Type`) |
| **Body** | The actual content: HTML, JSON, an image, and so on |

### HTTP is stateless

Each request is **independent**: the server does not automatically remember previous requests. To recognize a returning user, applications add their own mechanisms, such as cookies and tokens (Lesson 15). Statelessness is one reason web servers can be scaled to handle huge numbers of visitors (Lesson 18).

---

## 12.2 HTTP Methods and Status Codes

### HTTP methods

The **method** (or *verb*) states what the client wants to do. The most important ones:

| Method | Meaning | Typical use | Has a body? |
|---|---|---|---|
| **GET** | Read a resource | Fetch a page or a list | No |
| **POST** | Create something or submit data | Add a new task, submit a form | Yes |
| **PUT** | Replace a resource entirely | Overwrite a task with new data | Yes |
| **PATCH** | Change part of a resource | Update just the title | Yes |
| **DELETE** | Remove a resource | Delete a task | Usually no |
| **HEAD** | Like GET, but headers only | Check that something exists | No |
| **OPTIONS** | Ask what is allowed | Browser pre-checks for cross-origin requests | No |

Two properties help in reasoning about methods:

- A method is **safe** if it does not change anything on the server. `GET` and `HEAD` are safe, so browsers and search engines can call them freely.
- A method is **idempotent** if repeating it has the same effect as doing it once. `GET`, `PUT`, and `DELETE` are idempotent (deleting something twice leaves the same end state), but `POST` is not (posting twice creates two items).

> Never use `GET` to change data. Links can be prefetched or crawled, which would trigger unwanted changes.

### Status codes

The **status code** is a three-digit number in the response. The first digit gives the category:

| Range | Category | Meaning |
|---|---|---|
| **1xx** | Informational | The request is being processed |
| **2xx** | Success | It worked |
| **3xx** | Redirection | Go somewhere else |
| **4xx** | Client error | The *client* made a mistake |
| **5xx** | Server error | The *server* failed |

The codes you will use most:

| Code | Name | When to use it |
|---|---|---|
| **200** | OK | The request succeeded |
| **201** | Created | A new resource was created (usually after POST) |
| **204** | No Content | Success with nothing to return (often after DELETE) |
| **301** | Moved Permanently | The resource has a new permanent address |
| **302** | Found | Temporary redirect |
| **304** | Not Modified | The cached copy is still valid |
| **400** | Bad Request | The request is malformed or invalid |
| **401** | Unauthorized | Authentication is missing or invalid (the client is not logged in) |
| **403** | Forbidden | The client is known but not allowed to do this |
| **404** | Not Found | The resource does not exist |
| **405** | Method Not Allowed | That path does not support this method |
| **409** | Conflict | The request conflicts with current state (such as a duplicate) |
| **422** | Unprocessable Content | The data is well-formed but fails validation |
| **429** | Too Many Requests | The client is being rate limited |
| **500** | Internal Server Error | An unexpected error on the server |
| **502 / 503** | Bad Gateway / Service Unavailable | An upstream service failed or the server is overloaded or down |

Choosing accurate status codes matters: frontends, other services, and monitoring tools all rely on them to know what happened.

---

## 12.3 Designing REST Endpoints

**REST (Representational State Transfer)** is a widely used style for designing web APIs around **resources**. A resource is a "thing" your API manages, such as a task, a user, or an order. Each resource has an address (URL), and the HTTP method says what to do with it.

### Core conventions

1. **URLs name resources (nouns), not actions (verbs).** Use `/tasks`, not `/getTasks` or `/createTask`.
2. **Use plural nouns for collections.** `/tasks` is the collection; `/tasks/42` is one item in it.
3. **Let the HTTP method express the action.**
4. **Use hierarchy to show relationships.** `/users/7/tasks` means "the tasks belonging to user 7".
5. **Be stateless.** Each request must contain everything needed to process it.
6. **Return appropriate status codes** (Section 12.2).

### Mapping CRUD to HTTP

Most APIs offer the four basic data operations, abbreviated **CRUD** (Create, Read, Update, Delete):

| Operation | Method and path | Success response |
|---|---|---|
| List all tasks | `GET /tasks` | `200 OK` with a list |
| Read one task | `GET /tasks/42` | `200 OK`, or `404` if missing |
| Create a task | `POST /tasks` | `201 Created` with the new task |
| Replace a task | `PUT /tasks/42` | `200 OK` |
| Update part of a task | `PATCH /tasks/42` | `200 OK` |
| Delete a task | `DELETE /tasks/42` | `204 No Content` |

### An example API

Here is a small working REST API for tasks, using Express, with data held in memory for simplicity:

```javascript
const express = require("express");
const app = express();
app.use(express.json()); // parse JSON request bodies

let tasks = [{ id: 1, title: "Learn REST", done: false }];
let nextId = 2;

// GET /tasks - list the collection
app.get("/tasks", (req, res) => res.json(tasks));

// GET /tasks/:id - read one item
app.get("/tasks/:id", (req, res) => {
  const task = tasks.find((t) => t.id === Number(req.params.id));
  if (!task) return res.status(404).json({ error: "Task not found" });
  res.json(task);
});

// POST /tasks - create a new item
app.post("/tasks", (req, res) => {
  const task = { id: nextId++, title: req.body.title, done: false };
  tasks.push(task);
  res.status(201).json(task);
});

// DELETE /tasks/:id - remove an item
app.delete("/tasks/:id", (req, res) => {
  tasks = tasks.filter((t) => t.id !== Number(req.params.id));
  res.status(204).end();
});

app.listen(3000);
```

With the server running, these requests and responses show the design in action.

Listing tasks:

```bash
curl http://localhost:3000/tasks
```

```text
[{"id":1,"title":"Learn REST","done":false}]
```

Creating a task returns status `201`:

```bash
curl -i -X POST http://localhost:3000/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Ship it"}'
```

```text
HTTP/1.1 201 Created
{"id":2,"title":"Ship it","done":false}
```

Asking for a task that does not exist returns `404` with a helpful message:

```bash
curl http://localhost:3000/tasks/99
```

```text
{"error":"Task not found"} [404]
```

Deleting returns `204` and no body:

```bash
curl -X DELETE http://localhost:3000/tasks/1
```

```text
DELETE -> 204
```

### Filtering, sorting, and pagination

Use **query parameters** for optional adjustments to a collection, rather than inventing new paths:

```text
GET /tasks?done=false                  filter
GET /tasks?sort=due_date&order=asc     sort
GET /tasks?page=2&limit=20             paginate
```

**Pagination** matters because returning thousands of items at once is slow and wasteful. Return one page at a time, along with information on how to get the next.

### Versioning

APIs change, and existing clients (especially mobile apps that users do not update immediately) must not break. A common approach is to put a version in the path, such as `/v1/tasks`, so a `/v2/tasks` can introduce changes later while `/v1` continues to work.

### Consistent errors

Pick one format for error responses and use it everywhere, so clients can handle errors uniformly:

```json
{
  "error": "Task not found",
  "status": 404
}
```

### A note on other API styles

REST is the most common style, but not the only one. **GraphQL** lets clients specify exactly which fields they want from a single endpoint, and **gRPC** uses a compact binary format for fast communication between services. The same request-and-response foundations apply.

---

## 12.4 JSON and Data Formats

APIs need an agreed format for the data they exchange. The dominant format for web APIs is **JSON (JavaScript Object Notation)**: a lightweight, human-readable text format that every major language can read and write.

```json
{
  "id": 1,
  "title": "Learn JSON",
  "done": false,
  "tags": ["web", "api"],
  "due": null
}
```

### JSON's building blocks

| Type | Example |
|---|---|
| **Object** (key-value pairs) | `{"name": "Ada"}` |
| **Array** (ordered list) | `[1, 2, 3]` |
| **String** | `"hello"` |
| **Number** | `42`, `3.14` |
| **Boolean** | `true`, `false` |
| **Null** | `null` |

JSON is strict. Keys and strings **must use double quotes**, trailing commas are not allowed, and comments are not allowed.

### Serialization and parsing

Programs hold data as objects in memory, but only *text* can travel over the network. Converting an object to JSON text is called **serialization**; converting text back into an object is **parsing** (or deserialization).

In Python:

```python
import json

text = '{"id": 1, "title": "Learn JSON", "done": false, "tags": ["web", "api"], "due": null}'

task = json.loads(text)  # JSON text -> Python dict
print(task["title"], task["done"], task["due"])

print(json.dumps(task, indent=2))  # Python dict -> JSON text
```

Output:

```text
Learn JSON False None
{
  "id": 1,
  "title": "Learn JSON",
  "done": false,
  "tags": [
    "web",
    "api"
  ],
  "due": null
}
```

In JavaScript:

```javascript
const task = JSON.parse('{"id":1,"title":"Learn JSON","done":false}');
console.log(task.title);

console.log(JSON.stringify({ ...task, done: true }));
```

Output:

```text
Learn JSON
{"id":1,"title":"Learn JSON","done":true}
```

Backend frameworks do this for you. In the Express example, `res.json(task)` serializes the object, and `express.json()` parses incoming request bodies.

### The `Content-Type` header

HTTP uses the `Content-Type` header to tell the receiver how to interpret the body. For JSON, it is `application/json`. Sending JSON without this header, or sending the wrong one, is a frequent cause of "my body is empty" bugs.

### Other formats you will encounter

| Format | Content type | Used for |
|---|---|---|
| **HTML** | `text/html` | Pages rendered on the server |
| **Form data** | `application/x-www-form-urlencoded` | Traditional HTML form submissions |
| **Multipart form data** | `multipart/form-data` | File uploads |
| **XML** | `application/xml` | Older APIs and enterprise systems |
| **Protocol Buffers** | binary | gRPC and other high-performance communication |

### Practical tips for JSON APIs

- Represent dates and times as **ISO 8601** strings, such as `"2025-03-01T14:30:00Z"`, since JSON has no date type.
- Use `null` for "no value", and decide consistently whether a missing field and a `null` mean the same thing.
- Stick to one naming style for keys (`camelCase` or `snake_case`) across the whole API.
- Never trust the contents of an incoming request. Validate them (Lesson 13).

---

[Previous](./[11]-Styling-And-User-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[13]-Middleware-And-Request-Handling.md)
