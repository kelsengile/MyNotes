[Previous](./[6]-ES-Modules.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[8]-TypeScript-Basics.md)

*Intermediate JavaScript*

# Lesson 7 - Error Handling & Debugging in the Browser

## 7.1 Reading Errors in the Console

When JavaScript throws an uncaught error, it's printed to the DevTools Console along with a **stack trace** — the chain of function calls that led to the error. Learning to read a stack trace from the bottom up (where the code originated) or top down (where it failed) is one of the fastest ways to fix bugs without guessing.

---

## 7.2 try/catch/finally

Wrap risky code in `try` and handle failures in `catch`:

```js
try {
  const data = JSON.parse(input);
} catch (error) {
  console.error("Invalid JSON:", error.message);
} finally {
  hideLoadingSpinner(); // always runs, success or failure
}
```

`finally` runs regardless of whether an error was thrown, making it useful for cleanup like hiding a spinner or closing a connection.

---

## 7.3 Throwing and Custom Errors

You can throw your own errors, including custom Error subclasses that carry extra context:

```js
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = "ValidationError";
    this.field = field;
  }
}

throw new ValidationError("Email is required", "email");
```

Custom errors let calling code distinguish *what kind* of failure happened (`error instanceof ValidationError`) instead of parsing message strings.

---

## 7.4 Debugging with Breakpoints

Instead of sprinkling `console.log()` everywhere, DevTools lets you set **breakpoints** in the Sources panel: click a line number to pause execution there, then inspect variables, step line-by-line (`Step Over`, `Step Into`, `Step Out`), and watch how values change. The `debugger;` statement in code does the same thing programmatically, pausing execution wherever it's placed whenever DevTools is open.

---

## 7.5 Handling Errors in Async Code

Unhandled Promise rejections don't crash the page but do log a warning and silently skip logic. Always pair `await` with `try/catch`, and consider a global handler for anything that slips through:

```js
window.addEventListener("unhandledrejection", event => {
  console.error("Unhandled Promise rejection:", event.reason);
});
```

---

[Previous](./[6]-ES-Modules.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[8]-TypeScript-Basics.md)
