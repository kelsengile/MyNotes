[Previous](./[7]-Validation-And-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[9]-Authentication.md)

*Building APIs*

# Lesson 8 - Working With Databases

Until now the todo API stored data in an array that vanished on every restart. Real applications keep data in a database. This lesson shows how to connect Express to MongoDB and to SQL databases, how to manage connections safely, and how to keep data access code organized.

---

## 8.1 MongoDB with Mongoose

**MongoDB** is a document database: data is stored as JSON-like documents inside collections. **Mongoose** is a library that adds schemas, validation, and helpful query methods on top of the official MongoDB driver.

### Setup

You need a running MongoDB server. The easiest options are a free cluster on MongoDB Atlas or a local container:

```bash
docker run -d --name mongo -p 27017:27017 mongo:7
npm install mongoose
```

Add the connection string to `.env` (see Lesson 5):

```
MONGODB_URI=mongodb://localhost:27017/myapp
```

### Connecting

```js
// src/db/mongoose.js
import mongoose from 'mongoose';

export async function connectMongo(uri) {
  await mongoose.connect(uri);
  console.log('MongoDB connected');
}
```

### Defining a schema and model

A **schema** describes the shape of a document. A **model** is the class you use to read and write that collection.

```js
// src/models/Todo.js
import mongoose from 'mongoose';

const todoSchema = new mongoose.Schema(
  {
    title: { type: String, required: true, trim: true, minlength: 1, maxlength: 200 },
    done: { type: Boolean, default: false },
  },
  { timestamps: true }   // adds createdAt and updatedAt automatically
);

export const Todo = mongoose.model('Todo', todoSchema);
```

Mongoose pluralizes the model name, so `Todo` is stored in the `todos` collection.

### CRUD with Mongoose

```js
// Create
const todo = await Todo.create({ title: 'Learn Mongoose' });

// Read all (filter, sort, paginate)
const todos = await Todo.find({ done: false })
  .sort({ createdAt: -1 })
  .skip(0)
  .limit(10);

// Read one
const one = await Todo.findById(id);

// Update (new: true returns the updated document, runValidators checks the schema)
const updated = await Todo.findByIdAndUpdate(
  id,
  { done: true },
  { new: true, runValidators: true }
);

// Delete
await Todo.findByIdAndDelete(id);
```

### Using it in a route

Because Express 5 forwards async errors automatically (Lesson 7), handlers stay clean:

```js
// src/routes/todos.js
import { Router } from 'express';
import mongoose from 'mongoose';
import { Todo } from '../models/Todo.js';
import { NotFoundError, BadRequestError } from '../errors/AppError.js';

const router = Router();

router.get('/', async (req, res) => {
  res.json(await Todo.find().sort({ createdAt: -1 }));
});

router.get('/:id', async (req, res) => {
  if (!mongoose.isValidObjectId(req.params.id)) throw new BadRequestError('Invalid id');
  const todo = await Todo.findById(req.params.id);
  if (!todo) throw new NotFoundError('Todo not found');
  res.json(todo);
});

router.post('/', async (req, res) => {
  const todo = await Todo.create({ title: req.body?.title });
  res.status(201).json(todo);
});

export default router;
```

If `Todo.create` fails schema validation, Mongoose throws a `ValidationError`. Translate it in your central error handler:

```js
if (err.name === 'ValidationError') {
  return res.status(422).json({ error: 'Validation failed', details: Object.values(err.errors).map((e) => e.message) });
}
```

### Useful tips

| Tip | Why |
|---|---|
| Add `.lean()` to read-only queries | Returns plain objects instead of full Mongoose documents, which is faster |
| Use `select('-password')` | Leaves sensitive fields out of results |
| Validate ids with `mongoose.isValidObjectId` | An invalid id throws a `CastError` otherwise |
| Add indexes (`{ unique: true }` or `schema.index(...)`) | Speeds up lookups and enforces uniqueness |

A duplicate value on a unique index throws an error with `err.code === 11000`. Map it to a `409 Conflict` response.

---

## 8.2 SQL with Prisma or Sequelize

**SQL databases** (PostgreSQL, MySQL, SQLite) store data in tables with rows and columns, and relationships are expressed with foreign keys. In Node.js you rarely write every SQL statement by hand. An **ORM** (Object-Relational Mapper) or query layer does it for you.

| | **Prisma** | **Sequelize** |
|---|---|---|
| Style | Schema file plus generated, type-safe client | Models defined in JavaScript |
| Migrations | Built in (`prisma migrate`) | Separate `sequelize-cli` tool |
| TypeScript support | Excellent | Fair |
| Learning curve | Gentle | Moderate |
| Best for | New projects | Existing projects that already use it |

### Prisma

> **Version note:** The steps below use Prisma 6, installed with `prisma@6` and `@prisma/client@6`. Newer major versions changed parts of the setup (generator and driver configuration), so if you install a newer one, follow its official getting-started guide for those parts. The ideas stay the same.

```bash
npm install prisma@6 @prisma/client@6
npx prisma init --datasource-provider postgresql
```

This creates `prisma/schema.prisma` and adds `DATABASE_URL` to `.env`. Define your models there:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  name  String
  posts Post[]
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
  createdAt DateTime @default(now())
}
```

Create the tables and generate the client:

```bash
npx prisma migrate dev --name init
```

Every time you change `schema.prisma`, run `migrate dev` again. It records a migration file so your database history is reproducible.

Use the client:

```js
// src/db/prisma.js
import { PrismaClient } from '@prisma/client';

export const prisma = new PrismaClient();
```

```js
// Create
const user = await prisma.user.create({
  data: { email: 'ana@example.com', name: 'Ana' },
});

// Read many, with a relation included
const users = await prisma.user.findMany({
  where: { name: { contains: 'a', mode: 'insensitive' } },
  include: { posts: true },
  orderBy: { id: 'asc' },
  take: 10,
  skip: 0,
});

// Read one
const one = await prisma.user.findUnique({ where: { id: 1 } });

// Update
await prisma.user.update({ where: { id: 1 }, data: { name: 'Ana Maria' } });

// Delete
await prisma.user.delete({ where: { id: 1 } });
```

Prisma throws a `PrismaClientKnownRequestError` with a `code` property. Two you will meet often:

| Code | Meaning | Suggested HTTP status |
|---|---|---|
| `P2002` | Unique constraint failed | `409` |
| `P2025` | Record not found (for update or delete) | `404` |

**Transactions** make several writes succeed or fail together:

```js
await prisma.$transaction([
  prisma.user.create({ data: { email: 'b@example.com', name: 'Ben' } }),
  prisma.post.create({ data: { title: 'Hello', authorId: 1 } }),
]);
```

### Sequelize

```bash
npm install sequelize pg pg-hstore
```

```js
// src/db/sequelize.js
import { Sequelize, DataTypes } from 'sequelize';

export const sequelize = new Sequelize(process.env.DATABASE_URL, { logging: false });

export const User = sequelize.define('User', {
  email: { type: DataTypes.STRING, allowNull: false, unique: true },
  name: { type: DataTypes.STRING, allowNull: false },
});
```

```js
await sequelize.authenticate();   // check the connection
await sequelize.sync();           // create tables if missing (fine for learning, use migrations in production)

const user = await User.create({ email: 'ana@example.com', name: 'Ana' });
const all = await User.findAll({ where: { name: 'Ana' }, limit: 10 });
const one = await User.findByPk(1);
await one.update({ name: 'Ana Maria' });
await one.destroy();
```

Whichever tool you pick, never build SQL by joining strings with user input. ORMs use parameterized queries for you, and that is what prevents SQL injection (see Lesson 11).

---

## 8.3 Connection management

Opening a connection is slow, so you open it **once** when the app starts and reuse it for every request.

### Rules

1. **Create one client per process.** Export a single `prisma` or `sequelize` instance from one module, as shown above. Creating a new client inside each handler exhausts the database's connection limit.
2. **Connect before you listen.** Do not accept requests you cannot serve.
3. **Use connection pools.** Mongoose, Prisma, and Sequelize all keep a pool of connections automatically. You can tune it (for example `mongoose.connect(uri, { maxPoolSize: 10 })`).
4. **Close connections on shutdown.** This lets in-flight queries finish and avoids leaked connections.

### Start the app only after the database is ready

```js
// src/server.js
import app from './app.js';
import config from './config/index.js';
import { connectMongo } from './db/mongoose.js';
import mongoose from 'mongoose';

async function start() {
  await connectMongo(config.mongoUri);

  const server = app.listen(config.port, () => {
    console.log(`Listening on ${config.port}`);
  });

  async function shutdown(signal) {
    console.log(`${signal} received, shutting down`);
    server.close(async () => {          // stop accepting new requests
      await mongoose.connection.close(); // then close the database
      process.exit(0);
    });
  }

  process.on('SIGINT', () => shutdown('SIGINT'));
  process.on('SIGTERM', () => shutdown('SIGTERM'));
}

start().catch((err) => {
  console.error('Failed to start', err);
  process.exit(1);
});
```

For Prisma, the shutdown step is `await prisma.$disconnect()`. For Sequelize it is `await sequelize.close()`.

### Handling a lost connection

Mongoose reconnects automatically and emits events you can log:

```js
mongoose.connection.on('disconnected', () => console.warn('MongoDB disconnected'));
mongoose.connection.on('reconnected', () => console.log('MongoDB reconnected'));
```

A **health endpoint** (see Lesson 12) should check the database so a load balancer can tell when the app is unhealthy.

---

## 8.4 Structuring data access

Lesson 5 separated routes, controllers, and services. Databases add one more question: where does the database code live? If queries are scattered through services, changing databases or testing logic becomes painful.

### The repository pattern

A **repository** is a small module that holds all queries for one kind of data. Services call the repository and never touch the ORM directly.

```
Route  →  Controller  →  Service  →  Repository  →  Database
```

```js
// src/repositories/userRepository.js
import { prisma } from '../db/prisma.js';

export function findById(id) {
  return prisma.user.findUnique({ where: { id } });
}

export function findByEmail(email) {
  return prisma.user.findUnique({ where: { email } });
}

export function create(data) {
  return prisma.user.create({ data });
}

export function list({ skip, take }) {
  return prisma.user.findMany({ skip, take, orderBy: { id: 'asc' } });
}
```

```js
// src/services/userService.js
import * as userRepo from '../repositories/userRepository.js';
import { NotFoundError, ConflictError } from '../errors/AppError.js';

export async function getUser(id) {
  const user = await userRepo.findById(id);
  if (!user) throw new NotFoundError(`User ${id} not found`);
  return user;
}

export async function registerUser(data) {
  if (await userRepo.findByEmail(data.email)) {
    throw new ConflictError('Email already registered');
  }
  return userRepo.create(data);
}
```

The service holds the **business rules** (is the email taken?). The repository only knows **how to talk to the database**.

### Benefits

| Benefit | Explanation |
|---|---|
| Easy testing | In tests, replace the repository with a fake (see Lesson 13) |
| Easy to change | Swapping Mongoose for Prisma only touches repositories |
| No duplication | Common queries are written once |
| Clear boundaries | Each layer has one responsibility |

### Practical advice

- Keep **DTOs** (the shapes you return to clients) separate from database records. Never return a user record that includes a password hash.
- Put database-specific error translation (like `P2002`) in the repository or one central handler so services stay clean.
- For small projects, calling the ORM from services directly is acceptable. Introduce repositories when the app grows or when you want easier testing.
- Use **migrations** for SQL schema changes instead of editing tables by hand, and commit the migration files.

---

[Previous](./[7]-Validation-And-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[9]-Authentication.md)
