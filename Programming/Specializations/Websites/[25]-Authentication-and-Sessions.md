[Previous](./[24]-WebSockets-and-Realtime-Communication.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[26]-Connecting-Frontend-to-Backend.md)

*Full-Stack Concepts*

# Lesson 25 - Authentication & Sessions

## 25.1 Authentication vs Authorization

**Authentication** answers "who is this user?" (logging in, verifying a password). **Authorization** answers "what is this user allowed to do?" (can this user delete this post?). A system can authenticate someone correctly and still need to check authorization separately for every sensitive action.

---

## 25.2 Passwords Must Be Hashed

Passwords should never be stored as plain text. A **hashing** function (like `bcrypt`) transforms a password into a fixed-length, one-way scrambled value — it can be checked against but never reversed back into the original password:

```js
const bcrypt = require("bcrypt");

const hashed = await bcrypt.hash(plainPassword, 10);
// store `hashed` in the database, never `plainPassword`

const isValid = await bcrypt.compare(enteredPassword, hashed);
```

If a database is ever breached, hashed passwords remain (practically) useless to an attacker, unlike plain-text ones.

---

## 25.3 HTTP Is Stateless — Sessions Fix That

Each HTTP request is independent by default — the server has no memory of previous requests. **Sessions** solve this: after login, the server creates a session record and gives the browser a **session ID**, usually stored in a cookie, which is sent automatically on every subsequent request so the server can look up who's making it.

```js
app.post("/login", async (req, res) => {
  // verify credentials...
  req.session.userId = user.id; // express-session handles the cookie
  res.send("Logged in");
});
```

---

## 25.4 Token-Based Auth: JWTs

An alternative to server-stored sessions is a **JSON Web Token (JWT)** — a signed token containing user data, which the client stores and sends with each request (commonly in an `Authorization` header). The server verifies the token's signature instead of looking anything up in a session store, which scales more easily across multiple servers but makes tokens harder to revoke early since the server isn't tracking them centrally.

```js
const token = jwt.sign({ userId: user.id }, secretKey, { expiresIn: "1h" });
// client sends: Authorization: Bearer <token>
```

---

## 25.5 Protecting Routes

Whichever method is used, protected routes should check authentication (and authorization) before doing anything sensitive:

```js
function requireAuth(req, res, next) {
  if (!req.session.userId) {
    return res.status(401).json({ error: "Not authenticated" });
  }
  next();
}

app.delete("/posts/:id", requireAuth, (req, res) => { /* ... */ });
```

---

[Previous](./[24]-WebSockets-and-Realtime-Communication.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[26]-Connecting-Frontend-to-Backend.md)
