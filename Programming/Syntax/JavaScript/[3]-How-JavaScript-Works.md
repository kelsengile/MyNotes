[Previous](./[2]-Running-JavaScript-Code.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[4]-Package-Management.md)

*Getting Started*

# Lesson 3 - How JavaScript Works

## 3.1 The JavaScript Engine

Every browser and Node.js contains a **JavaScript engine** — a program that reads your JavaScript code and executes it. Chrome and Node.js both use Google's **V8** engine; Firefox uses **SpiderMonkey**; Safari uses **JavaScriptCore**. The engine's job is to parse your code, convert it into something the computer can execute quickly, and run it.

You don't need to know engine internals to write JavaScript, but understanding two ideas — the **call stack** and the **event loop** — will make confusing bugs (especially with asynchronous code, later in this course) much easier to reason about.

---

## 3.2 JavaScript Is Single-Threaded

JavaScript runs on a **single thread**, meaning it can only do one thing at a time. Unlike some languages that run multiple pieces of code simultaneously, JavaScript executes one line, finishes it, then moves to the next.

This matters because a single slow operation can freeze everything else — a webpage stops responding, or a server stops handling other requests — until that operation completes.

---

## 3.3 The Call Stack

The **call stack** keeps track of what function is currently running. When a function is called, it's pushed onto the stack; when it finishes, it's popped off.

```js
function greet() {
  sayHi();
}

function sayHi() {
  console.log("Hi!");
}

greet();
```

Execution order: `greet()` is pushed → it calls `sayHi()`, which is pushed on top → `sayHi()` finishes and pops off → `greet()` finishes and pops off. If a function never finishes (like infinite, unstoppable recursion), the stack overflows and JavaScript throws a `RangeError: Maximum call stack size exceeded`.

---

## 3.4 The Event Loop (A First Look)

If JavaScript can only do one thing at a time, how does it handle things like waiting for a file to download without freezing everything? That's the job of the **event loop**.

Slow operations — network requests, timers, file reads — are handed off to the browser or Node.js itself to handle in the background. Once they finish, their follow-up code is placed in a **queue**. The event loop constantly checks: *"Is the call stack empty? If so, take the next item from the queue and run it."*

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

console.log("3");
```

This logs `1`, `3`, `2` — even with a `0` millisecond delay — because `setTimeout`'s callback always waits in the queue until the current code (`console.log("1")` and `console.log("3")`) finishes and the stack is empty. Lesson 27 revisits this in depth once you've learned functions and promises.

---

## 3.5 Interpreted, Then Compiled

JavaScript is often called an "interpreted" language, but modern engines are smarter than that: they use **JIT (Just-In-Time) compilation**, which compiles frequently-run code into fast machine code while the program is running. You don't control this process directly, but it's why modern JavaScript performs far better than older versions of the language.

[Previous](./[2]-Running-JavaScript-Code.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[4]-Package-Management.md)
