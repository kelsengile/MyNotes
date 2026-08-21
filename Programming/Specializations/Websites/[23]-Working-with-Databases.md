[Previous](./[22]-Building-a-REST-API.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[24]-WebSockets-and-Realtime-Communication.md)

*Back-End Basics*

# Lesson 23 - Working with Databases (SQL vs NoSQL basics)

## 23.1 Why Not Just Use Variables?

The in-memory `users` array from Lesson 22 disappears the moment the server restarts, and can't be shared across multiple server instances. A **database** persists data to disk (or a managed remote service), survives restarts, and supports efficient searching, filtering, and relationships between records at a scale plain JavaScript objects can't handle.

---

## 23.2 SQL (Relational) Databases

**SQL databases** (PostgreSQL, MySQL, SQLite) store data in **tables** with a fixed schema — defined columns and types — and rows representing individual records. Tables can be related to each other (a `posts` table referencing a `users` table via a `user_id` column), and data is queried using **SQL** (Structured Query Language):

```sql
SELECT id, name FROM users WHERE id = 1;

INSERT INTO users (name, email) VALUES ('Ada', 'ada@example.com');
```

Relational databases excel when data has clear structure and relationships that need to stay consistent.

---

## 23.3 NoSQL (Document) Databases

**NoSQL databases** (MongoDB being the most common example) store data as flexible, often JSON-like documents instead of rigid rows and columns. There's no fixed schema enforced by the database itself — documents in the same collection can have different fields:

```js
db.users.insertOne({ name: "Ada", interests: ["math", "computing"] });
db.users.find({ name: "Ada" });
```

This flexibility suits rapidly evolving data shapes, but pushes more responsibility for consistency onto the application code.

---

## 23.4 Choosing Between Them

Relational databases are generally preferred when data is highly structured and relationships must stay strictly consistent (e.g. financial records, inventory). Document databases are often preferred for less structured or rapidly changing data (e.g. content management, logging). Many real applications use both for different parts of the system — there's no universal winner.

---

## 23.5 Connecting from Node.js

Applications typically talk to a database through a driver or **ORM (Object-Relational Mapper)**, which lets you interact with the database using JavaScript objects instead of raw query strings:

```js
// Example using Prisma, a popular ORM
const user = await prisma.user.findUnique({ where: { id: 1 } });
```

ORMs also help prevent a serious security issue — **SQL injection** — by safely handling values passed into queries instead of concatenating raw strings, which is covered further in Lesson 36.

---

[Previous](./[22]-Building-a-REST-API.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[24]-WebSockets-and-Realtime-Communication.md)
