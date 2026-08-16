[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[13]-Arrays-and-Array-Methods.md)

*Core Syntax*

# Lesson 12 - Error Handling

## 12.1 What Happens When Code Throws

When JavaScript hits code it can't execute — calling a method on `undefined`, dividing by an invalid operation, referencing an undeclared variable — it **throws an error**, and execution stops immediately unless something catches it:

```js
console.log("Before");
null.toUpperCase();       // TypeError: Cannot read properties of null
console.log("After");     // never runs
```

Without handling, an uncaught error crashes a Node.js script or stops the current operation in a browser.

---

## 12.2 try, catch, finally

Wrap risky code in a `try` block; if it throws, execution jumps to `catch` instead of crashing:

```js
try {
  const data = JSON.parse("{ invalid json");
  console.log(data);
} catch (error) {
  console.log("Something went wrong:", error.message);
}

console.log("Program continues");
```

`finally` runs regardless of whether an error occurred — commonly used for cleanup, like closing a file or connection:

```js
try {
  riskyOperation();
} catch (error) {
  console.log("Failed:", error.message);
} finally {
  console.log("Cleanup runs no matter what");
}
```

---

## 12.3 The throw Statement

You can raise your own errors with `throw`, typically to reject invalid input early:

```js
function divide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero");
  }
  return a / b;
}

try {
  divide(10, 0);
} catch (error) {
  console.log(error.message); // "Cannot divide by zero"
}
```

`throw` can technically be used with any value, but always throw an `Error` object (or a subclass) — it automatically captures a stack trace, which is invaluable for debugging.

---

## 12.4 Built-in Error Types

JavaScript has several built-in error subclasses that describe common failure categories:

```js
new TypeError("wrong type");     // e.g. calling a method on the wrong kind of value
new RangeError("out of range");  // e.g. an invalid array length
new ReferenceError("undefined variable"); // referencing something that doesn't exist
new SyntaxError("bad syntax");    // usually thrown by JavaScript itself, rarely by you
```

Every error object has a `.name` and a `.message` property, which you can inspect in a `catch` block to react differently depending on what went wrong.

---

## 12.5 Custom Error Classes

For larger applications, it's common to define your own error types by extending the built-in `Error` class (class syntax is introduced fully in Lesson 18):

```js
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

function setAge(age) {
  if (age < 0) {
    throw new ValidationError("Age cannot be negative");
  }
  return age;
}

try {
  setAge(-5);
} catch (error) {
  if (error instanceof ValidationError) {
    console.log("Validation failed:", error.message);
  } else {
    throw error; // re-throw anything we didn't expect
  }
}
```

Custom error classes make it possible to `catch` and handle specific failure types differently, rather than treating every error the same way.

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[13]-Arrays-and-Array-Methods.md)
