[Previous](./[3]-Anatomy-of-a-Web-Project.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[5]-Fetch-API.md)

*Intermediate JavaScript*

# Lesson 4 - Asynchronous JavaScript

## 4.1 Why Asynchronous Code Exists

JavaScript runs on a **single thread** — it can only do one thing at a time. But some operations (network requests, file reads, timers) take time to complete, and freezing the whole page while waiting would ruin the user experience. Asynchronous code lets JavaScript start a slow operation, keep running other code, and come back to handle the result once it's ready, all without blocking the thread.

---

## 4.2 The Event Loop, Briefly

JavaScript uses an **event loop** to manage this. Synchronous code runs first, on the **call stack**. Asynchronous tasks (like a `fetch` finishing or a `setTimeout` firing) get queued, and the event loop only pulls them onto the call stack once it's empty. This is why `setTimeout(fn, 0)` doesn't run immediately — it waits for all currently running synchronous code to finish first.

---

## 4.3 Callbacks

The original pattern for async code was passing a **callback** — a function to run once an operation finishes:

```js
setTimeout(() => {
  console.log("Done waiting");
}, 1000);
```

Callbacks work but become hard to read when chained (nesting request after request), a pattern often called "callback hell."

---

## 4.4 Promises

A **Promise** represents a value that will exist in the future — either resolved (success) or rejected (failure). Promises can be chained with `.then()` and `.catch()`:

```js
fetchData()
  .then(result => process(result))
  .catch(error => console.error(error));
```

Promises fix callback nesting by letting you chain steps linearly instead of nesting them.

---

## 4.5 Async/Await

`async`/`await` is syntax built on top of Promises that lets asynchronous code read like synchronous code:

```js
async function loadData() {
  try {
    const result = await fetchData();
    process(result);
  } catch (error) {
    console.error(error);
  }
}
```

`await` pauses execution *within that function* until the Promise settles, without blocking the rest of the page. Any function using `await` must be declared `async`, and errors are caught with a regular `try/catch` block.

---

## 4.6 Running Things in Parallel

Awaiting Promises one after another is sequential and slow if they don't depend on each other. `Promise.all()` runs multiple Promises concurrently and waits for all of them:

```js
const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()]);
```

If any Promise passed to `Promise.all()` rejects, the whole thing rejects immediately — use `Promise.allSettled()` if you want results even when some fail.

---

[Previous](./[3]-Anatomy-of-a-Web-Project.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[5]-Fetch-API.md)
