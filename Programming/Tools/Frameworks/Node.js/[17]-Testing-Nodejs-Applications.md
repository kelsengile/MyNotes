[Previous](./[16]-Debugging-And-Logging.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[18]-Security-And-Performance.md)

*Quality And Delivery*

# Lesson 17 - Testing Node.js Applications

Automated tests check that your code works and keep it working as it changes. This lesson covers the built-in test runner, popular frameworks, mocking, and testing asynchronous code.

## 17.1 The built-in test runner

Node.js includes a test runner in the `node:test` module. No installation is needed.

```js
// math.js
export function add(a, b) {
  return a + b;
}
```

```js
// math.test.js
import { test, describe } from "node:test";
import assert from "node:assert/strict";
import { add } from "./math.js";

describe("add", () => {
  test("adds two numbers", () => {
    assert.equal(add(2, 3), 5);
  });

  test("handles negatives", () => {
    assert.equal(add(-1, -1), -2);
  });
});
```

Run all tests:

```bash
node --test
node --test --watch          # re-run on changes
node --test --experimental-test-coverage   # show code coverage
```

Common `assert` methods:

| Method | Checks |
|---|---|
| `assert.equal(a, b)` | Values are equal (strict in `assert/strict`) |
| `assert.deepEqual(a, b)` | Objects or arrays have equal contents |
| `assert.ok(value)` | Value is truthy |
| `assert.throws(fn)` | Function throws an error |
| `assert.rejects(promise)` | Promise rejects |

Test files are found automatically when they are named `*.test.js` or live in a `test` folder.

---

## 17.2 Jest and Vitest

Third-party frameworks add features like richer matchers, snapshots, and watch UIs.

- **Jest:** a long-established, all-in-one framework.
- **Vitest:** a modern, fast framework with a Jest-compatible API and great ESM and TypeScript support.

```bash
npm install -D vitest
```

```js
// math.test.js
import { describe, it, expect } from "vitest";
import { add } from "./math.js";

describe("add", () => {
  it("adds two numbers", () => {
    expect(add(2, 3)).toBe(5);
  });

  it("compares objects", () => {
    expect({ a: 1 }).toEqual({ a: 1 });
  });

  it("checks errors", () => {
    expect(() => JSON.parse("{")).toThrow();
  });
});
```

```json
{ "scripts": { "test": "vitest run" } }
```

Common matchers: `toBe`, `toEqual`, `toBeTruthy`, `toContain`, `toThrow`, `toHaveLength`, `toHaveBeenCalledWith`.

Which one should you pick? The built-in runner is great for small projects and libraries with no dependencies. Choose Vitest or Jest if you want snapshots, easy mocking, or browser-like environments.

---

## 17.3 Mocking and stubbing

Tests should be fast and reliable, so they should not call real databases, send real emails, or depend on the clock. A **mock** or **stub** replaces a real dependency with a controlled fake.

- **Stub:** returns fixed values.
- **Spy:** records how it was called.
- **Mock:** a fake with built-in expectations.

With the built-in runner:

```js
import { test, mock } from "node:test";
import assert from "node:assert/strict";

test("calls the logger", () => {
  const logger = { info: mock.fn() };

  function doWork(log) {
    log.info("working");
  }
  doWork(logger);

  assert.equal(logger.info.mock.callCount(), 1);
  assert.deepEqual(logger.info.mock.calls[0].arguments, ["working"]);
});
```

Faking time so tests do not need to wait:

```js
import { test, mock } from "node:test";

test("timer", () => {
  mock.timers.enable({ apis: ["setTimeout"] });
  let called = false;
  setTimeout(() => (called = true), 5000);
  mock.timers.tick(5000);
  assert.equal(called, true);
});
```

With Vitest: `vi.fn()` creates a mock, `vi.spyOn(obj, "method")` spies, and `vi.mock("./module")` replaces a whole module.

Tip: design code so dependencies are **passed in** as parameters. It makes them easy to replace in tests. Do not over-mock, because a test that mocks everything proves very little.

---

## 17.4 Testing async code

Tests can be `async` functions. Return or `await` the promise so the runner waits for it.

```js
import { test } from "node:test";
import assert from "node:assert/strict";

async function fetchUser(id) {
  if (id <= 0) throw new Error("Invalid id");
  return { id, name: "Ana" };
}

test("resolves a user", async () => {
  const user = await fetchUser(1);
  assert.equal(user.name, "Ana");
});

test("rejects for bad id", async () => {
  await assert.rejects(fetchUser(0), { message: "Invalid id" });
});
```

Common mistake: forgetting `await`. The test finishes before the promise settles, and passes even if the code is broken.

Testing an HTTP server without a fixed port:

```js
import { test, before, after } from "node:test";
import assert from "node:assert/strict";
import http from "node:http";

let server, base;

before(async () => {
  server = http.createServer((req, res) => res.end("ok"));
  await new Promise((r) => server.listen(0, r));  // port 0 = random free port
  base = `http://localhost:${server.address().port}`;
});

after(() => server.close());

test("GET responds", async () => {
  const res = await fetch(base);
  assert.equal(await res.text(), "ok");
});
```

Good testing habits: keep tests independent, give them descriptive names, test behavior rather than internal details, and cover edge cases like empty input and failures.

[Previous](./[16]-Debugging-And-Logging.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[18]-Security-And-Performance.md)