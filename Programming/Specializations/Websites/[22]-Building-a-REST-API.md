[Previous](./[21]-Servers-and-Node.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[23]-Working-with-Databases.md)

*Back-End Basics*

# Lesson 22 - Building a Simple REST API

## 22.1 What REST Means

**REST** (Representational State Transfer) is a set of conventions for designing web APIs around **resources** (nouns, like "users" or "posts") and standard HTTP methods that act on them, rather than inventing a new endpoint name for every action. A well-designed REST API is predictable: once you know the pattern, you can guess most of the endpoints.

---

## 22.2 Resources and HTTP Methods

| Method | Endpoint | Meaning |
|---|---|---|
| GET | `/users` | list all users |
| GET | `/users/1` | get user with id 1 |
| POST | `/users` | create a new user |
| PUT | `/users/1` | replace user 1 entirely |
| PATCH | `/users/1` | partially update user 1 |
| DELETE | `/users/1` | delete user 1 |

The URL identifies *what* resource you're acting on; the HTTP method identifies *what action* to take.

---

## 22.3 A Minimal API with Express

```js
const express = require("express");
const app = express();
app.use(express.json());

let users = [{ id: 1, name: "Ada" }];

app.get("/users", (req, res) => {
  res.json(users);
});

app.post("/users", (req, res) => {
  const newUser = { id: users.length + 1, name: req.body.name };
  users.push(newUser);
  res.status(201).json(newUser);
});

app.get("/users/:id", (req, res) => {
  const user = users.find(u => u.id === Number(req.params.id));
  if (!user) return res.status(404).json({ error: "Not found" });
  res.json(user);
});

app.listen(3000);
```

---

## 22.4 Status Codes That Matter

- `200 OK` — success
- `201 Created` — a resource was successfully created
- `400 Bad Request` — the client sent invalid data
- `401 Unauthorized` / `403 Forbidden` — auth issues (Lesson 25)
- `404 Not Found` — resource doesn't exist
- `500 Internal Server Error` — something broke on the server

Returning accurate status codes (not just `200` for everything) lets clients handle different outcomes correctly without parsing response bodies to guess what happened.

---

## 22.5 Validating Input

Never trust data coming from a client. Before acting on request data, validate it — check required fields are present, types are correct, and values are within acceptable ranges — and respond with `400` and a clear error message when they aren't:

```js
app.post("/users", (req, res) => {
  if (!req.body.name || typeof req.body.name !== "string") {
    return res.status(400).json({ error: "name is required" });
  }
  // ... proceed
});
```

---

[Previous](./[21]-Servers-and-Node.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[23]-Working-with-Databases.md)
