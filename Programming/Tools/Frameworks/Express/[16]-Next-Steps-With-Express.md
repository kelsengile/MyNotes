[Previous](./[15]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Express.md)

*Hardening And Delivery*

# Lesson 16 - Next Steps With Express

You can now build, secure, test, and deploy an Express API. This final lesson looks forward: adding TypeScript for safer code, seeing how NestJS builds on the ideas you already know, documenting your API so others can use it, and projects to practice on.

---

## 16.1 TypeScript with Express

**TypeScript** is JavaScript with types. You describe the shape of your data, and the compiler catches mistakes (a misspelled property, a missing argument, `null` where a value was expected) before the code runs. Large Express projects commonly use it.

### Setup

```bash
mkdir my-ts-api && cd my-ts-api
npm init -y
npm install express
npm install --save-dev typescript tsx @types/node @types/express
npx tsc --init
```

| Package | Purpose |
|---|---|
| `typescript` | The compiler (`tsc`) |
| `tsx` | Runs TypeScript files directly in development, with watch mode |
| `@types/node`, `@types/express` | Type definitions for Node.js and Express. Express is written in JavaScript, so the types come separately |

Match the `@types/express` major version to your Express version. Express 5 uses `@types/express` version 5.

### A practical `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

`"strict": true` turns on the checks that make TypeScript worthwhile. Keep it on.

With `"type": "module"` in `package.json` and `NodeNext`, relative imports must still end in `.js`, even though the source file is `.ts`:

```ts
import usersRouter from './routes/users.js';   // refers to users.ts after compilation
```

### Scripts

```json
{
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "typecheck": "tsc --noEmit"
  }
}
```

During development `tsx` skips type checking for speed, so run `npm run typecheck` (and in CI) to catch type errors. For production you compile with `tsc` and run the JavaScript in `dist/`.

### A typed server

```ts
// src/app.ts
import express, { type Request, type Response, type NextFunction } from 'express';

const app = express();
app.use(express.json());

app.get('/health', (req: Request, res: Response) => {
  res.json({ status: 'ok' });
});

export default app;
```

### Typing request data

`Request` accepts generics for params, response body, request body, and query, in that order: `Request<Params, ResBody, ReqBody, Query>`.

```ts
interface CreateTodoBody {
  title: string;
}

interface TodoParams {
  id: string;     // route params are always strings (Lesson 2)
}

interface Todo {
  id: number;
  title: string;
  done: boolean;
}

app.post('/todos', (req: Request<{}, Todo, CreateTodoBody>, res: Response<Todo>) => {
  const todo: Todo = { id: 1, title: req.body.title, done: false };
  res.status(201).json(todo);        // the compiler checks the shape matches Todo
});

app.get('/todos/:id', (req: Request<TodoParams>, res: Response) => {
  const id = Number(req.params.id);  // req.params.id is typed as string
  res.json({ id });
});
```

> Types exist only at compile time. TypeScript **cannot** guarantee what a client actually sends. `req.body` claiming to be `CreateTodoBody` is a promise you must verify with runtime validation (Lesson 7).

### Zod: one schema for validation and types

Zod solves that gap. Define the schema once, validate at runtime, and **infer** the TypeScript type from it:

```ts
import { z } from 'zod';

export const createTodoSchema = z.object({
  title: z.string().min(1).max(200),
});

export type CreateTodoBody = z.infer<typeof createTodoSchema>;
// { title: string }

app.post('/todos', (req, res) => {
  const result = createTodoSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(422).json({ error: 'Validation failed', details: result.error.issues });
  }

  const body: CreateTodoBody = result.data;   // safe and typed
  res.status(201).json({ id: 1, ...body, done: false });
});
```

### Typed middleware and error handlers

```ts
import type { RequestHandler, ErrorRequestHandler } from 'express';

export const requireApiKey: RequestHandler = (req, res, next) => {
  if (req.get('x-api-key') !== process.env.API_KEY) {
    res.status(401).json({ error: 'Invalid API key' });
    return;                    // RequestHandler returns void, so return without a value
  }
  next();
};

export const errorHandler: ErrorRequestHandler = (err, req, res, next) => {
  if (res.headersSent) return next(err);
  res.status(err.statusCode ?? 500).json({ error: err.message });
};
```

### Adding properties to `req` (like `req.user`)

Lesson 9 attached `req.user`. TypeScript rejects that until you extend the type with **declaration merging**:

```ts
// src/types/express.d.ts
export {};

declare global {
  namespace Express {
    interface Request {
      user?: { id: number; role: 'user' | 'admin' };
    }
  }
}
```

Make sure `tsconfig.json` includes that folder (here `"include": ["src"]` already does).

### Typed configuration

```ts
// src/config.ts
function required(name: string): string {
  const value = process.env[name];
  if (!value) throw new Error(`Missing required environment variable: ${name}`);
  return value;
}

export const config = {
  port: Number(process.env.PORT ?? 3000),
  databaseUrl: required('DATABASE_URL'),
  isProduction: process.env.NODE_ENV === 'production',
} as const;
```

### Tips

- Start with `strict: true`. Loosening it later is easier than tightening it.
- Avoid `any`. Use `unknown` when you truly do not know a type, then narrow it.
- Prisma (Lesson 8) generates types from your schema automatically, so database results are typed for free.
- Vitest works with TypeScript without extra setup (Lesson 13).
- For deployment, add a build stage to your Dockerfile: install all dependencies, run `npm run build`, then copy `dist/` and production dependencies into the final image (Lesson 15).

---

## 16.2 Moving to NestJS

**NestJS** is a TypeScript framework for building server applications. By default it runs **on top of Express** (it can also use Fastify). Everything you learned still applies underneath: requests, responses, middleware, status codes, and the HTTP concepts of REST. Nest adds **structure** and conventions that Express leaves up to you.

### Express vs NestJS

| | **Express** | **NestJS** |
|---|---|---|
| Philosophy | Minimal, unopinionated | Opinionated, structured |
| Language | JavaScript or TypeScript | TypeScript first |
| Project structure | You decide (Lesson 5) | Defined by modules, controllers, and providers |
| Validation | Add Zod or Joi yourself | Pipes, commonly with `class-validator` or Zod |
| Dependency injection | None built in | Built in |
| Testing | You set it up | Built-in utilities |
| Learning curve | Gentle | Steeper |
| Best for | Small to medium APIs, learning, flexibility | Large teams and big applications |

### Mapping the concepts you know

| What you built in Express | NestJS equivalent |
|---|---|
| Router file (`routes/users.js`) | **Controller** with `@Controller('users')` |
| Service file (Lesson 5) | **Provider** (a class marked `@Injectable()`) |
| Folder grouping a feature's files | **Module** (`@Module({...})`) |
| Middleware | **Middleware**, **Guards**, **Interceptors** |
| `authenticate` / `requireRole` middleware | **Guards** (`@UseGuards(...)`) |
| Validation middleware (Lesson 7) | **Pipes** (`ValidationPipe`) |
| Central error handler | **Exception filters** |
| Manually passing services into functions | **Dependency injection** through constructors |

### What Nest code looks like

```ts
// users.controller.ts
import { Controller, Get, Post, Body, Param, NotFoundException } from '@nestjs/common';
import { UsersService } from './users.service';

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}   // injected automatically

  @Get()
  findAll() {
    return this.usersService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    const user = this.usersService.findOne(Number(id));
    if (!user) throw new NotFoundException(`User ${id} not found`);
    return user;
  }

  @Post()
  create(@Body() body: { name: string }) {
    return this.usersService.create(body);
  }
}
```

```ts
// users.service.ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UsersService {
  private users = [{ id: 1, name: 'Ana' }];

  findAll() { return this.users; }
  findOne(id: number) { return this.users.find((u) => u.id === id); }
  create(data: { name: string }) {
    const user = { id: this.users.length + 1, ...data };
    this.users.push(user);
    return user;
  }
}
```

```ts
// users.module.ts
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

Notice the pattern is the same three layers from Lesson 5 (route, controller, service). Nest names them and wires them for you. Returning a value sends JSON, and throwing `NotFoundException` becomes a `404`, just like your custom `AppError` classes in Lesson 7.

### Should you switch?

| Stay with Express when | Consider NestJS when |
|---|---|
| The project is small or medium | The codebase and team are large |
| You want full control and few dependencies | You want enforced conventions so everyone writes code the same way |
| You are still learning the fundamentals | You are comfortable with TypeScript and classes |
| Your app is a focused service | You need many features with consistent structure (auth, queues, WebSockets, microservices) |

**Learning order that works well:** master Express first (you now have), add TypeScript, then try Nest. Knowing what Nest does behind the scenes makes it far less magical.

### Getting started

```bash
npm install -g @nestjs/cli
nest new my-nest-app
cd my-nest-app
npm run start:dev
```

Follow the official NestJS documentation for the current setup details, since the CLI and templates evolve.

---

## 16.3 API documentation with OpenAPI

An API nobody can understand is an API nobody uses. **OpenAPI** (formerly Swagger) is the standard format for describing REST APIs: endpoints, parameters, request and response shapes, and authentication. From one description you can generate interactive documentation, client libraries, and tests.

### A minimal OpenAPI document

OpenAPI documents are written in YAML or JSON:

```yaml
# openapi.yaml
openapi: 3.0.3
info:
  title: Todo API
  version: 1.0.0
  description: A simple API for managing todos.

servers:
  - url: http://localhost:3000/api

paths:
  /todos:
    get:
      summary: List todos
      parameters:
        - name: page
          in: query
          schema: { type: integer, minimum: 1, default: 1 }
      responses:
        '200':
          description: A list of todos
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Todo'

    post:
      summary: Create a todo
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NewTodo'
      responses:
        '201':
          description: The created todo
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Todo'
        '422':
          description: Validation failed

  /todos/{id}:
    get:
      summary: Get one todo
      parameters:
        - name: id
          in: path
          required: true
          schema: { type: integer }
      responses:
        '200':
          description: The todo
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Todo'
        '404':
          description: Todo not found

components:
  schemas:
    Todo:
      type: object
      required: [id, title, done]
      properties:
        id: { type: integer, example: 1 }
        title: { type: string, example: Learn Express }
        done: { type: boolean, example: false }
    NewTodo:
      type: object
      required: [title]
      properties:
        title: { type: string, minLength: 1, maxLength: 200 }

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

To mark an endpoint as protected, add `security: [ { bearerAuth: [] } ]` to it. The `$ref` entries reuse schemas so you define each shape once.

### Serving interactive docs with Swagger UI

```bash
npm install swagger-ui-express yaml
```

```js
import swaggerUi from 'swagger-ui-express';
import YAML from 'yaml';
import fs from 'node:fs';

const spec = YAML.parse(fs.readFileSync('./openapi.yaml', 'utf8'));

app.use('/docs', swaggerUi.serve, swaggerUi.setup(spec));
```

Visit `http://localhost:3000/docs` to browse every endpoint and try requests in the browser. Helmet's strict CSP (Lesson 11) can block Swagger UI's assets. Adjust the policy for the `/docs` path only, or serve the docs only in non-production environments.

### Three ways to keep docs correct

Documentation that drifts from the code is worse than none. Choose a strategy that keeps them together:

| Approach | How it works | Trade-off |
|---|---|---|
| **Design first** | Write `openapi.yaml` first, then implement it | Great for teams, but you must keep code and file in sync |
| **Generated from code comments** | `swagger-jsdoc` reads `@openapi` comments above your routes | Docs sit next to the code, but comments are not checked by the compiler |
| **Generated from schemas** | Libraries such as `zod-to-openapi` build the document from your Zod schemas | One source of truth for validation, types, and documentation |

The schema-driven approach pairs well with Lesson 7: the same Zod schema validates requests, types your code (16.1), and documents the API.

### Checking the document

- Use a linter such as **Spectral** to enforce consistency (every endpoint has a summary and error responses).
- Use the contract in tests: tools can send requests to your app and verify the responses match the described schemas (Lesson 13).
- Generate typed API clients for front ends with tools such as `openapi-typescript`.

### Writing good documentation

- Describe **every** response status, especially errors (`400`, `401`, `404`, `422`).
- Include realistic `example` values.
- Document authentication clearly, including how to obtain a token.
- Document pagination, filtering, rate limits, and versioning rules (Lesson 6).
- Add a short "Getting started" section in the README that shows a first request with `curl`.

---

## 16.4 Project ideas

The fastest way to learn is to build. Each project below combines several lessons. Start with a small version, then add features.

### Beginner

| Project | What to build | Lessons practiced |
|---|---|---|
| **Todo API** | Full CRUD with a database, validation, and tests | 2, 3, 6, 7, 8, 13 |
| **URL shortener** | `POST /shorten` returns a short code. `GET /:code` redirects. Count visits | 2, 3, 8 |
| **Bookmarks manager** | Save links with tags. Filter and search by tag. Paginate | 6, 8 |
| **Notes API with auth** | Users register and log in, and see only their own notes | 7, 8, 9 |

### Intermediate

| Project | What to build | Lessons practiced |
|---|---|---|
| **Blog platform** | Posts, comments, categories, roles (author and admin), image uploads, server-rendered or JSON | 5, 8, 9, 10, 11 |
| **Link-in-bio or portfolio generator** | Users create pages from templates and upload images | 9, 10 |
| **Expense tracker** | Categories, monthly summaries, CSV export by streaming, charts data endpoint | 6, 8, 10 |
| **Job board API** | Companies post jobs, applicants apply with a resume upload, email notifications through a queue | 8, 9, 10, 14 |
| **Weather dashboard backend** | Call an external weather API, cache results in Redis, rate limit clients | 11, 14 |

### Advanced

| Project | What to build | Lessons practiced |
|---|---|---|
| **Real-time chat** | Rooms, private messages, online status, message history, authenticated sockets | 9, 14 |
| **Task board (Trello-style)** | Boards, lists, cards, live updates for all viewers | 8, 14 |
| **E-commerce API** | Products, carts, orders, payments through a provider's test mode, webhooks (use `express.raw()` for signature checks), background email receipts | 7, 8, 9, 14 |
| **Image processing service** | Upload images, a queue resizes them, results stored in object storage, a status endpoint | 10, 14, 15 |
| **Multi-tenant SaaS starter** | Organizations, invitations, role-based access, API keys, usage rate limits, OpenAPI docs | 9, 11, 16 |

### Make any project production-ready

Work through this checklist on at least one project, since it is where most of the learning happens:

1. Split into routes, controllers, services, and repositories (Lessons 5 and 8).
2. Validate every input with Zod or Joi and use custom error classes (Lesson 7).
3. Add Helmet, CORS, and rate limiting (Lesson 11).
4. Add structured logging and health endpoints (Lesson 12).
5. Write unit and integration tests and run them in CI (Lesson 13).
6. Convert it to TypeScript (Lesson 16.1).
7. Document it with OpenAPI (Lesson 16.3).
8. Write a Dockerfile and deploy it behind HTTPS (Lesson 15).

### Where to keep learning

| Topic | Why it matters |
|---|---|
| **Official Express documentation** (expressjs.com) | The authoritative reference, including the Express 5 migration guide |
| **Node.js documentation** (nodejs.org) | Streams, the event loop, and worker threads explain how Express behaves |
| **HTTP fundamentals** | Caching, content negotiation, and status codes appear in every API |
| **Databases in depth** | Indexing, transactions, and query performance decide how fast your app is |
| **Security** (OWASP Top 10) | A checklist of the most common web vulnerabilities |
| **GraphQL or tRPC** | Alternatives to REST for some use cases |
| **Other frameworks** (Fastify, Hono, NestJS) | Seeing different approaches deepens your understanding |
| **Reading open-source Express projects** | Shows how experienced developers organize real applications |

Congratulations on finishing the Express Topic. Pick a project, start small, and keep shipping.

---

[Previous](./[15]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Express.md)
