[Previous](./[4]-The-Event-Loop.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[6]-Events-And-EventEmitter.md)

*Asynchronous Node*

# Lesson 5 - Asynchronous Patterns

Node.js code constantly waits for results. This lesson covers the three main ways to write asynchronous code and how to handle errors in each.

## 5.1 Callbacks and callback hell

A **callback** is a function you pass to another function to be called when work finishes. Node.js uses the **error-first** convention: the first argument is an error (or `null`), the second is the result.

```js
const fs = require("node:fs");

fs.readFile("notes.txt", "utf8", (err, data) => {
  if (err) {
    console.error("Failed:", err.message);
    return;
  }
  console.log(data);
});
```

When tasks depend on each other, callbacks nest deeper and deeper. This is called **callback hell** or the pyramid of doom:

```js
fs.readFile("a.txt", "utf8", (err, a) => {
  if (err) return console.error(err);
  fs.readFile("b.txt", "utf8", (err, b) => {
    if (err) return console.error(err);
    fs.writeFile("c.txt", a + b, (err) => {
      if (err) return console.error(err);
      console.log("done");
    });
  });
});
```

This is hard to read and error handling is repeated at every level. Promises solve this.

---

## 5.2 Promises

A **Promise** represents a value that will exist in the future. It is in one of three states: **pending**, **fulfilled**, or **rejected**.

```js
const fs = require("node:fs/promises");

fs.readFile("a.txt", "utf8")
  .then((a) => {
    console.log(a);
    return fs.readFile("b.txt", "utf8");
  })
  .then((b) => console.log(b))
  .catch((err) => console.error("Failed:", err.message))
  .finally(() => console.log("finished"));
```

Creating your own promise:

```js
function wait(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
```

Combining promises:

- `Promise.all([...])`: waits for all, rejects if any fails.
- `Promise.allSettled([...])`: waits for all, never rejects, reports each outcome.
- `Promise.race([...])`: settles with the first one to finish.
- `Promise.any([...])`: fulfills with the first success.

Convert an old callback function into a promise with `util.promisify`:

```js
const { promisify } = require("node:util");
const readFile = promisify(require("node:fs").readFile);
```

---

## 5.3 async and await

`async`/`await` is syntax that lets promise-based code read like normal top-to-bottom code.

```js
const fs = require("node:fs/promises");

async function combine() {
  const a = await fs.readFile("a.txt", "utf8");
  const b = await fs.readFile("b.txt", "utf8");
  await fs.writeFile("c.txt", a + b);
  return "done";
}

combine().then(console.log);
```

Rules:

- An `async` function always returns a promise.
- `await` can only be used inside an `async` function (or at the top level of an ES module).
- `await` pauses only that function, not the whole program.

Run independent tasks in parallel instead of one after another:

```js
// Slow: sequential
const a = await fs.readFile("a.txt", "utf8");
const b = await fs.readFile("b.txt", "utf8");

// Fast: parallel
const [a2, b2] = await Promise.all([
  fs.readFile("a.txt", "utf8"),
  fs.readFile("b.txt", "utf8"),
]);
```

---

## 5.4 Error handling in async code

Each style handles errors differently:

```js
// Callbacks: check err
fs.readFile("x", (err, data) => {
  if (err) return handle(err);
});

// Promises: .catch
fs.promises.readFile("x").catch(handle);

// async/await: try/catch
async function run() {
  try {
    await fs.promises.readFile("x");
  } catch (err) {
    console.error(err.code); // e.g. "ENOENT"
  }
}
```

Important points:

- A `try/catch` does **not** catch errors thrown inside a callback that runs later.
- Always handle rejections. An unhandled promise rejection crashes modern Node.js processes.
- Use a global safety net for logging, but do not rely on it for normal handling:

```js
process.on("unhandledRejection", (reason) => {
  console.error("Unhandled rejection:", reason);
  process.exit(1);
});
```

- Create meaningful errors with `throw new Error("message")` and, when useful, `new Error("message", { cause: originalError })`.

[Previous](./[4]-The-Event-Loop.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[6]-Events-And-EventEmitter.md)