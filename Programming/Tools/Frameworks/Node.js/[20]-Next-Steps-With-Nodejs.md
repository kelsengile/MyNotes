[Previous](./[19]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md)

*Quality And Delivery*

# Lesson 20 - Next Steps With Node.js

You now understand how Node.js works: the event loop, modules, core APIs, tooling, testing, and deployment. This final lesson shows where to go next.

## 20.1 Express and NestJS

Building a server with only `http` means writing routing, body parsing, and error handling yourself. Frameworks do that for you.

**Express** is the most widely used Node.js web framework. It is small, flexible, and has a huge ecosystem.

```js
// npm install express
import express from "express";

const app = express();
app.use(express.json());

app.get("/", (req, res) => res.send("Hello"));

app.post("/users", (req, res) => {
  const { name } = req.body;
  res.status(201).json({ id: 1, name });
});

app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ error: "Server error" });
});

app.listen(3000, () => console.log("Listening on 3000"));
```

Express concepts: **routes**, **middleware** (functions that run in sequence for each request), and **error handlers**.

**NestJS** is a structured, opinionated framework built on top of Express (or Fastify) and TypeScript. It uses **modules**, **controllers**, **services**, and **dependency injection**, which helps large teams keep big codebases organized.

| | Express | NestJS |
|---|---|---|
| Style | Minimal, flexible | Structured, opinionated |
| Language | JavaScript or TypeScript | TypeScript-first |
| Learning curve | Gentle | Steeper |
| Best for | Small to medium APIs, prototypes | Large applications and teams |

Other options worth knowing: **Fastify** (fast and schema-focused), **Hono**, and **Koa**. Full-stack frameworks such as **Next.js** also run on Node.js.

---

## 20.2 TypeScript with Node

**TypeScript** adds static types to JavaScript. It catches many mistakes before the code runs, and improves editor autocomplete.

```ts
interface User {
  id: number;
  name: string;
}

function greet(user: User): string {
  return `Hello, ${user.name}`;
}

greet({ id: 1, name: "Ana" });
// greet({ id: 1 });  // Error: property 'name' is missing
```

Setup:

```bash
npm install -D typescript @types/node tsx
npx tsc --init
```

Running during development:

```bash
npx tsx watch src/index.ts      # run and restart on changes
```

Building for production:

```bash
npx tsc                         # compile .ts files to .js
node dist/index.js
```

Recent Node.js versions can also run simple TypeScript files directly by stripping the types, but a `tsc` build step is still the standard for production projects and for checking types. Install `@types/node` so TypeScript knows Node.js's built-in APIs, and add `"strict": true` to `tsconfig.json`.

---

## 20.3 Microservices and queues

A **monolith** is one application containing everything. **Microservices** split a system into small independent services (users, orders, payments), each deployed and scaled separately and talking over the network.

| | Monolith | Microservices |
|---|---|---|
| Complexity | Lower | Higher |
| Deployment | One unit | Many units |
| Scaling | Whole app | Per service |
| Team independence | Less | More |

Start with a well-organized monolith. Move to microservices only when team size or scaling needs truly justify the extra complexity.

**Message queues** let parts of a system communicate asynchronously. A producer adds a job to the queue and a consumer processes it later. This is useful for sending emails, resizing images, generating reports, or handling traffic spikes without making users wait.

Common tools:

- **BullMQ** (uses Redis) for job queues in Node.js.
- **RabbitMQ**: a general message broker.
- **Apache Kafka**: high-volume event streaming.
- **Cloud queues**: AWS SQS, Google Pub/Sub.

```js
// npm install bullmq   (requires a running Redis)
import { Queue, Worker } from "bullmq";

const connection = { host: "localhost", port: 6379 };

const emailQueue = new Queue("emails", { connection });
await emailQueue.add("welcome", { to: "ana@example.com" });

new Worker("emails", async (job) => {
  console.log("Sending email to", job.data.to);
}, { connection });
```

Services can also talk through **REST** APIs, **GraphQL**, or **gRPC**. Make jobs **idempotent** (safe to run twice), because queues may deliver a message more than once.

---

## 20.4 Building real projects

Reading is not enough. Build things. Project ideas, from easier to harder:

1. **A command-line to-do app** that stores tasks in a JSON file (Lessons 7, 8, 15).
2. **A file organizer** that sorts a folder by file type.
3. **A REST API for notes** with Express and SQLite or PostgreSQL (Lessons 10, 14, 20.1).
4. **A URL shortener** with a database, validation, and rate limiting.
5. **A real-time chat** with WebSockets.
6. **A web scraper or data pipeline** using streams and a job queue.
7. **A full app with authentication**, tests, Docker, and deployment.

For each project, practice the full cycle:

- Plan the features and data model.
- Organize code into modules.
- Configure through environment variables.
- Add logging and error handling.
- Write tests.
- Check security (`npm audit`, input validation).
- Containerize and deploy.
- Monitor it and fix what you find.

Keep learning:

- Read the official docs at [nodejs.org/docs](https://nodejs.org/docs/latest/api/).
- Read the source code of small open-source packages.
- Contribute to open-source projects.
- Follow Node.js release notes to learn about new built-in features.

The best way to improve is to build, break, debug, and repeat.

[Previous](./[19]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md)