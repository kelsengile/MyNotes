[Previous](./[12]-Logging-And-Debugging.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[14]-Real-Time-And-Advanced-Features.md)

*Hardening And Delivery*

# Lesson 13 - Testing Express Applications

Tests are small programs that check your code still works. They let you change things without fear. This lesson shows how to test individual functions, how to test your whole API without starting a real server, how to replace the database during tests, and how to organize everything.

---

## 13.1 Unit tests with Jest or Vitest

A **unit test** checks one small piece of logic in isolation, such as a service function. No HTTP and no database are involved, which makes unit tests fast.

### Choosing a test runner

| | **Vitest** | **Jest** |
|---|---|---|
| ES modules (`import`) | Works out of the box | Needs extra configuration or flags |
| Speed | Very fast | Fast |
| Syntax | Almost identical to Jest | The long-time standard |
| Best for | New ES-module projects (this Topic) | Existing projects that already use it |

This Topic uses **ES modules**, so the examples use **Vitest**. Moving to Jest is easy: the `describe`, `it`, and `expect` functions are the same, and only the mocking helpers change (`jest.fn` instead of `vi.fn`).

### Setup

```bash
npm install --save-dev vitest
```

Add a script to `package.json`:

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest"
  }
}
```

`vitest run` runs once and exits (good for CI). `vitest` stays running and re-runs tests when files change.

### Your first test

Imagine this pure function in `src/utils/pagination.js`:

```js
export function getPagination(pageInput, limitInput) {
  const page = Math.max(1, Number(pageInput) || 1);
  const limit = Math.min(100, Math.max(1, Number(limitInput) || 10));
  return { page, limit, skip: (page - 1) * limit };
}
```

Test it in `tests/unit/pagination.test.js`:

```js
import { describe, it, expect } from 'vitest';
import { getPagination } from '../../src/utils/pagination.js';

describe('getPagination', () => {
  it('uses defaults when nothing is provided', () => {
    expect(getPagination(undefined, undefined)).toEqual({ page: 1, limit: 10, skip: 0 });
  });

  it('calculates skip from page and limit', () => {
    expect(getPagination('3', '5')).toEqual({ page: 3, limit: 5, skip: 10 });
  });

  it('caps limit at 100', () => {
    expect(getPagination('1', '5000').limit).toBe(100);
  });

  it('ignores invalid numbers', () => {
    expect(getPagination('abc', '-4')).toEqual({ page: 1, limit: 1, skip: 0 });
  });
});
```

Test files end in `.test.js` so the runner finds them.

### The structure of a test

| Piece | Purpose |
|---|---|
| `describe('name', () => {...})` | Groups related tests |
| `it('does something', () => {...})` | One test case (`test` is an alias) |
| `expect(value).matcher(...)` | An assertion that fails the test if false |
| `beforeEach` / `afterEach` | Setup and cleanup that run around each test |

### Common matchers

```js
expect(2 + 2).toBe(4);                       // strict equality (===)
expect({ a: 1 }).toEqual({ a: 1 });          // deep equality for objects and arrays
expect(list).toContain('x');
expect(list).toHaveLength(3);
expect(value).toBeNull();
expect(value).toBeUndefined();
expect(value).toBeTruthy();
expect(fn).toThrow('message');               // function throws
await expect(promise).rejects.toThrow();     // async function rejects
await expect(promise).resolves.toBe(5);
```

### Testing services that throw

Using the service and custom errors from Lessons 5 and 7:

```js
import { describe, it, expect } from 'vitest';
import { getUser } from '../../src/services/userService.js';
import { NotFoundError } from '../../src/errors/AppError.js';

describe('getUser', () => {
  it('returns the user when it exists', () => {
    expect(getUser(1)).toMatchObject({ id: 1 });
  });

  it('throws NotFoundError when missing', () => {
    expect(() => getUser(999)).toThrow(NotFoundError);
  });
});
```

### Testing a middleware by itself

Middleware are just functions, so you can call them with fake objects. `vi.fn()` creates a **mock function** that records how it was called.

```js
import { describe, it, expect, vi } from 'vitest';
import { requireRole } from '../../src/middleware/requireRole.js';

describe('requireRole', () => {
  it('calls next() when the role matches', () => {
    const req = { user: { role: 'admin' } };
    const res = {};
    const next = vi.fn();

    requireRole('admin')(req, res, next);

    expect(next).toHaveBeenCalledOnce();
  });

  it('responds 403 when the role does not match', () => {
    const req = { user: { role: 'user' } };
    const json = vi.fn();
    const res = { status: vi.fn(() => ({ json })) };
    const next = vi.fn();

    requireRole('admin')(req, res, next);

    expect(res.status).toHaveBeenCalledWith(403);
    expect(next).not.toHaveBeenCalled();
  });
});
```

Writing fake `res` objects gets tedious. For anything involving routing, use the integration tests in the next section.

---

## 13.2 Integration tests with Supertest

An **integration test** checks several parts working together: routing, middleware, validation, and handlers. **Supertest** sends real HTTP requests to your Express app **without opening a network port**.

This is why Lesson 1 split `app.js` (builds and exports the app) from `server.js` (calls `listen`). Tests import `app` only.

```bash
npm install --save-dev supertest
```

### A first API test

```js
// tests/integration/todos.test.js
import { describe, it, expect } from 'vitest';
import request from 'supertest';
import app from '../../src/app.js';

describe('GET /api/todos', () => {
  it('returns a list of todos', async () => {
    const res = await request(app).get('/api/todos');

    expect(res.status).toBe(200);
    expect(res.headers['content-type']).toMatch(/json/);
    expect(Array.isArray(res.body)).toBe(true);
  });
});
```

### Testing each kind of request

```js
// POST with a JSON body (Supertest sets the Content-Type for you)
const res = await request(app)
  .post('/api/todos')
  .send({ title: 'Write tests' });

expect(res.status).toBe(201);
expect(res.body).toMatchObject({ title: 'Write tests', done: false });
expect(res.headers.location).toMatch(/\/api\/todos\/\d+/);

// Headers (for example authentication)
await request(app)
  .get('/api/me')
  .set('Authorization', `Bearer ${token}`)
  .expect(200);

// Query strings
await request(app).get('/api/todos').query({ page: 2, limit: 5 }).expect(200);

// Cookies
const agent = request.agent(app);           // an agent remembers cookies between requests
await agent.post('/login').send({ email, password }).expect(200);
await agent.get('/profile').expect(200);    // the session cookie is sent automatically
```

`.expect(200)` is a shortcut that fails the test if the status differs.

### Test the failure paths too

Good tests cover what happens when things go wrong:

```js
describe('POST /api/todos validation', () => {
  it('returns 400 when title is missing', async () => {
    const res = await request(app).post('/api/todos').send({});

    expect(res.status).toBe(400);
    expect(res.body.error).toMatch(/title/i);
  });

  it('returns 404 for an unknown route', async () => {
    const res = await request(app).get('/api/nothing-here');
    expect(res.status).toBe(404);
  });

  it('returns 401 without a token', async () => {
    await request(app).get('/api/me').expect(401);
  });
});
```

### Keeping tests independent

Each test should work no matter what order the tests run in. Reset shared state in `beforeEach`:

```js
import { beforeEach } from 'vitest';

beforeEach(async () => {
  await resetDatabase();      // clear tables or collections, then insert known data
});
```

Never let one test depend on data left behind by another.

### Environment variables for tests

Vitest sets `NODE_ENV` to `test` automatically. Give the app a separate configuration so tests never touch real data:

```
# .env.test
DATABASE_URL=postgres://user:password@localhost:5432/myapp_test
JWT_SECRET=test-secret
```

Load it from your Vitest config or setup file, and never point tests at a development or production database.

---

## 13.3 Mocking databases

Tests that use a real database are slower and need the database running. Often you want to **replace** it. There are three common options.

### Option 1: Mock the repository or service (unit tests)

Because Lesson 8 separated repositories from services, you can swap the repository for a fake. `vi.mock` replaces a whole module for the test file:

```js
import { describe, it, expect, vi, beforeEach } from 'vitest';
import * as userRepo from '../../src/repositories/userRepository.js';
import { registerUser } from '../../src/services/userService.js';
import { ConflictError } from '../../src/errors/AppError.js';

vi.mock('../../src/repositories/userRepository.js');   // every export becomes a mock function

describe('registerUser', () => {
  beforeEach(() => {
    vi.resetAllMocks();                                  // clean slate before each test
  });

  it('throws ConflictError if the email exists', async () => {
    userRepo.findByEmail.mockResolvedValue({ id: 1, email: 'a@b.com' });

    await expect(registerUser({ email: 'a@b.com', name: 'A' })).rejects.toThrow(ConflictError);
    expect(userRepo.create).not.toHaveBeenCalled();
  });

  it('creates the user when the email is free', async () => {
    userRepo.findByEmail.mockResolvedValue(null);
    userRepo.create.mockResolvedValue({ id: 2, email: 'new@b.com', name: 'New' });

    const user = await registerUser({ email: 'new@b.com', name: 'New' });

    expect(user.id).toBe(2);
    expect(userRepo.create).toHaveBeenCalledWith({ email: 'new@b.com', name: 'New' });
  });
});
```

Useful mock helpers:

| Helper | Meaning |
|---|---|
| `mockReturnValue(x)` | Returns `x` |
| `mockResolvedValue(x)` | Returns a promise that resolves to `x` |
| `mockRejectedValue(err)` | Returns a promise that rejects with `err` |
| `toHaveBeenCalledWith(...)` | Checks the arguments it received |
| `toHaveBeenCalledTimes(n)` | Checks how many times it ran |

### Option 2: Mock inside an API test

You can combine `vi.mock` with Supertest to test routes without any database:

```js
import request from 'supertest';
import { vi, it, expect } from 'vitest';
import app from '../../src/app.js';
import * as userService from '../../src/services/userService.js';

vi.mock('../../src/services/userService.js');

it('GET /api/users/1 returns the user', async () => {
  userService.getUser.mockResolvedValue({ id: 1, name: 'Ana' });

  const res = await request(app).get('/api/users/1');

  expect(res.status).toBe(200);
  expect(res.body).toEqual({ id: 1, name: 'Ana' });
});
```

This tests routing, middleware, and the controller while the service is faked.

### Option 3: A real but disposable database

Mocks can hide real problems, such as a wrong query. For better confidence, use a genuine database that exists only for the tests.

**MongoDB: `mongodb-memory-server`** runs a real MongoDB in memory:

```bash
npm install --save-dev mongodb-memory-server
```

```js
import mongoose from 'mongoose';
import { MongoMemoryServer } from 'mongodb-memory-server';
import { beforeAll, afterAll, afterEach } from 'vitest';

let mongo;

beforeAll(async () => {
  mongo = await MongoMemoryServer.create();
  await mongoose.connect(mongo.getUri());
});

afterEach(async () => {
  await mongoose.connection.dropDatabase();     // clean between tests
});

afterAll(async () => {
  await mongoose.disconnect();
  await mongo.stop();
});
```

**SQL: a separate test database or container.** Run migrations against `myapp_test` (for Prisma: `npx prisma migrate deploy` with the test `DATABASE_URL`), and clear tables between tests. A Docker container, or a library such as Testcontainers, can start a fresh database for each test run.

### Which option to use

| Situation | Choose |
|---|---|
| Testing business rules in a service | Mock the repository (Option 1) |
| Testing routes, validation, status codes | Mock the service (Option 2) or use a test database |
| Testing that queries and migrations actually work | A real disposable database (Option 3) |

A healthy project uses all three: many fast unit tests, a smaller number of API tests, and a few tests against a real database.

### Mocking other things

| Need | Approach |
|---|---|
| Current time | `vi.useFakeTimers()` and `vi.setSystemTime(...)` |
| External HTTP APIs | Mock the module that calls them, or use a tool such as `msw` |
| Sending email | Mock the mail-sending module and assert it was called |
| Random values and ids | Mock `crypto.randomUUID` or inject the value |

Avoid over-mocking. If a test mocks everything, it only proves your mocks work.

---

## 13.4 Test organization

### Folder layout

```
my-express-app/
├── src/
│   ├── app.js
│   ├── server.js
│   └── ...
└── tests/
    ├── setup.js                  # shared setup (environment, database)
    ├── helpers/
    │   ├── createTestUser.js     # reusable helpers
    │   └── authHeader.js
    ├── unit/
    │   ├── pagination.test.js
    │   └── userService.test.js
    └── integration/
        ├── todos.test.js
        └── auth.test.js
```

Some teams put each `*.test.js` file next to the code it tests instead. Either approach works. Pick one and be consistent.

### Vitest configuration

```js
// vitest.config.js
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    environment: 'node',
    include: ['tests/**/*.test.js'],
    setupFiles: ['./tests/setup.js'],
    coverage: {
      provider: 'v8',
      include: ['src/**/*.js'],
      exclude: ['src/server.js'],
    },
  },
});
```

### Naming tests

Describe **behavior**, so a failing test reads like a sentence.

```js
// Vague
it('works', ...)

// Clear
it('returns 404 when the todo does not exist', ...)
```

### The Arrange-Act-Assert pattern

Structure each test in three clear steps:

```js
it('creates a todo', async () => {
  // Arrange: prepare data
  const payload = { title: 'Buy milk' };

  // Act: do the thing
  const res = await request(app).post('/api/todos').send(payload);

  // Assert: check the outcome
  expect(res.status).toBe(201);
  expect(res.body.title).toBe('Buy milk');
});
```

### Helpers for repeated setup

```js
// tests/helpers/authHeader.js
import jwt from 'jsonwebtoken';

export function authHeader(user = { id: 1, role: 'user' }) {
  const token = jwt.sign({ sub: String(user.id), role: user.role }, process.env.JWT_SECRET);
  return { Authorization: `Bearer ${token}` };
}
```

```js
await request(app).get('/api/me').set(authHeader()).expect(200);
```

### Coverage

Coverage reports show which lines your tests execute:

```bash
npm install --save-dev @vitest/coverage-v8
npx vitest run --coverage
```

Use coverage to **find untested code**, not as a score to maximize. 100% coverage with weak assertions proves little.

### What to test

| Test | Skip |
|---|---|
| Business rules and edge cases | Trivial getters and setters |
| Every route's success and error responses | The internals of libraries (Express, Zod, Mongoose) |
| Authentication and authorization (can the wrong user get in?) | Exact wording of log messages |
| Bugs you have fixed, so they stay fixed | Implementation details that change often |

### The testing pyramid

```
        /\
       /  \      Few: end-to-end tests (full app, real browser or deployed environment)
      /----\
     /      \    Some: integration tests (API with Supertest, test database)
    /--------\
   /          \  Many: unit tests (functions and services, fast)
  /------------\
```

Many fast, small tests at the bottom. Fewer slow, broad tests at the top.

### Running tests automatically

Run your tests in continuous integration so broken code cannot be merged. A minimal GitHub Actions workflow in `.github/workflows/test.yml`:

```yaml
name: Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm test
```

### Test checklist

- Tests are independent and can run in any order.
- Tests never touch a real production or development database.
- Each test checks one behavior and has a descriptive name.
- Failure cases (400, 401, 403, 404, 409) are tested, not only success.
- `npm test` runs in CI on every push.

---

[Previous](./[12]-Logging-And-Debugging.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[14]-Real-Time-And-Advanced-Features.md)
