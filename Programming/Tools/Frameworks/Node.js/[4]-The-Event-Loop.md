[Previous](./[3]-Modules-And-npm.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[5]-Asynchronous-Patterns.md)

*Asynchronous Node*

# Lesson 4 - The Event Loop

The event loop is the core idea that makes Node.js efficient. Understanding it explains why some code is fast, why some code freezes your server, and in what order things run.

## 4.1 Single-threaded model

Your JavaScript runs on **one main thread**, executing one piece of code at a time. That sounds slow, but most programs spend their time waiting on files, databases, or networks, not computing.

Node.js handles this by **not waiting**. When you start a slow task, Node.js hands it to the operating system or to libuv's thread pool, keeps running other code, and comes back when the result is ready.

```js
console.log("1: start");

setTimeout(() => console.log("3: timer finished"), 1000);

console.log("2: end of script");
// Output order: 1, 2, 3
```

Think of a waiter in a restaurant: they take an order, give it to the kitchen, then serve other tables instead of standing and waiting for the food.

---

## 4.2 Event loop phases

The event loop repeatedly cycles through phases. Each phase has a queue of callbacks to run.

1. **Timers:** runs callbacks from `setTimeout` and `setInterval` whose time has elapsed.
2. **Pending callbacks:** runs some deferred system callbacks.
3. **Idle, prepare:** internal use only.
4. **Poll:** retrieves new I/O events and runs I/O callbacks. Waits here if nothing else is scheduled.
5. **Check:** runs `setImmediate` callbacks.
6. **Close callbacks:** runs cleanup callbacks like `socket.on("close")`.

```js
const fs = require("node:fs");

fs.readFile(__filename, () => {
  setTimeout(() => console.log("timeout"), 0);
  setImmediate(() => console.log("immediate"));
});
// Inside an I/O callback, "immediate" always prints before "timeout".
```

A timer's delay is a minimum, not a guarantee. If the thread is busy, the callback runs later.

---

## 4.3 Microtasks and nextTick

Between phases, Node.js runs two special queues that take priority over normal callbacks:

- **`process.nextTick` queue:** runs first.
- **Promise microtask queue:** runs right after (`.then`, `await` continuations).

```js
setTimeout(() => console.log("timeout"), 0);
setImmediate(() => console.log("immediate"));
Promise.resolve().then(() => console.log("promise"));
process.nextTick(() => console.log("nextTick"));
console.log("sync");

// sync, nextTick, promise, timeout, immediate
// (timeout and immediate order can vary in the main module)
```

The order is: synchronous code, then `nextTick`, then promises, then the event loop phases.

Warning: calling `nextTick` or creating promises endlessly can starve the event loop, because the loop cannot move on until those queues are empty.

---

## 4.4 Blocking vs non-blocking code

**Blocking** code stops everything else until it finishes. Because there is only one main thread, blocking it freezes your whole server.

```js
const fs = require("node:fs");

// Blocking: nothing else runs until the file is read
const data = fs.readFileSync("big.txt", "utf8");

// Non-blocking: other code runs while the file is read
fs.readFile("big.txt", "utf8", (err, data) => {
  if (err) throw err;
  console.log("done");
});
```

CPU-heavy work also blocks, even without I/O:

```js
// Blocks the event loop for several seconds
function slow() {
  let n = 0;
  for (let i = 0; i < 5e9; i++) n += i;
  return n;
}
```

Guidelines:

- Prefer asynchronous APIs in servers and anything handling many users.
- Synchronous APIs are fine in startup code and simple scripts.
- Move heavy computation to worker threads (see Lesson 11) or split it into small chunks.

[Previous](./[3]-Modules-And-npm.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[5]-Asynchronous-Patterns.md)