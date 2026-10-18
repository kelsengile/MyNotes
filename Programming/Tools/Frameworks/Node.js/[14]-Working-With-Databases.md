[Previous](./[13]-Environment-And-Configuration.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[15]-Building-Command-Line-Tools.md)

*Tooling And Projects*

# Lesson 14 - Working With Databases

Most applications must store data permanently. This lesson explains how Node.js connects to databases, the choice between drivers and ORMs, why connection pools matter, and shows a complete CRUD example.

## 14.1 Connecting to SQL and NoSQL

**SQL databases** store data in tables with rows and columns and a fixed schema. Examples: PostgreSQL, MySQL, SQLite. They are good for related data and transactions.

**NoSQL databases** use other models. MongoDB stores flexible JSON-like documents, Redis stores key-value pairs in memory. They are good for flexible data shapes, caching, or very large scale.

Each database has its own npm package:

| Database | Common package |
|---|---|
| PostgreSQL | `pg` |
| MySQL | `mysql2` |
| SQLite | `better-sqlite3` (or built-in `node:sqlite` in recent Node.js) |
| MongoDB | `mongodb` |
| Redis | `redis` |

Connecting to PostgreSQL:

```js
// npm install pg
const { Client } = require("pg");

const client = new Client({ connectionString: process.env.DATABASE_URL });
await client.connect();

const result = await client.query("SELECT NOW()");
console.log(result.rows[0]);

await client.end();
```

Keep the connection string in an environment variable (see Lesson 13), never in the code.

---

## 14.2 Drivers vs ORMs

A **driver** talks directly to the database. You write the queries yourself.

```js
const { rows } = await pool.query(
  "SELECT * FROM users WHERE email = $1",
  [email]
);
```

An **ORM** (Object-Relational Mapper) or query builder lets you use JavaScript objects and methods instead of writing query text.

```js
// Example with Prisma
const user = await prisma.user.findUnique({ where: { email } });
```

| | Driver | ORM / query builder |
|---|---|---|
| Control | Full | Less |
| Learning curve | Need to know the query language | Need to learn the library |
| Boilerplate | More | Less |
| Schema and migrations | Manual | Often built in |
| Examples | `pg`, `mysql2`, `mongodb` | Prisma, Sequelize, Drizzle, Mongoose, Knex |

**Always use parameterized queries** (the `$1` placeholders above). Never build queries by joining user input into a string. That causes **SQL injection**:

```js
// DANGEROUS
await pool.query(`SELECT * FROM users WHERE email = '${email}'`);
```

---

## 14.3 Connection pooling

Opening a database connection is slow. A **connection pool** keeps several connections open and reuses them, so requests do not each pay that cost, and the database is not overwhelmed by too many connections.

```js
const { Pool } = require("pg");

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 10,                    // maximum connections in the pool
  idleTimeoutMillis: 30000,   // close idle connections after 30s
});

// pool.query borrows a connection and returns it automatically
const { rows } = await pool.query("SELECT * FROM users");
```

For transactions (several queries that must succeed together) check out one connection manually:

```js
const client = await pool.connect();
try {
  await client.query("BEGIN");
  await client.query("UPDATE accounts SET balance = balance - 50 WHERE id = $1", [1]);
  await client.query("UPDATE accounts SET balance = balance + 50 WHERE id = $1", [2]);
  await client.query("COMMIT");
} catch (err) {
  await client.query("ROLLBACK");
  throw err;
} finally {
  client.release(); // always give the connection back
}
```

Create **one pool** when the app starts and share it. Do not create a new pool per request. Close it with `pool.end()` on shutdown.

---

## 14.4 Basic CRUD example

CRUD means **C**reate, **R**ead, **U**pdate, **D**elete. Here it is with PostgreSQL and a `users` table.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL
);
```

```js
const { Pool } = require("pg");
const pool = new Pool({ connectionString: process.env.DATABASE_URL });

// Create
async function createUser(name, email) {
  const { rows } = await pool.query(
    "INSERT INTO users (name, email) VALUES ($1, $2) RETURNING *",
    [name, email]
  );
  return rows[0];
}

// Read
async function getUser(id) {
  const { rows } = await pool.query("SELECT * FROM users WHERE id = $1", [id]);
  return rows[0] || null;
}

async function listUsers() {
  const { rows } = await pool.query("SELECT * FROM users ORDER BY id");
  return rows;
}

// Update
async function updateUser(id, name) {
  const { rows } = await pool.query(
    "UPDATE users SET name = $1 WHERE id = $2 RETURNING *",
    [name, id]
  );
  return rows[0] || null;
}

// Delete
async function deleteUser(id) {
  const result = await pool.query("DELETE FROM users WHERE id = $1", [id]);
  return result.rowCount > 0;
}

async function main() {
  const user = await createUser("Ana", "ana@example.com");
  console.log(await getUser(user.id));
  await updateUser(user.id, "Ana Maria");
  console.log(await listUsers());
  await deleteUser(user.id);
  await pool.end();
}
main().catch(console.error);
```

Notice the pattern: each operation is a small `async` function, queries are parameterized, and `RETURNING *` gives back the affected row.

[Previous](./[13]-Environment-And-Configuration.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[15]-Building-Command-Line-Tools.md)