[Previous](./[15]-Building-Command-Line-Tools.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[17]-Testing-Nodejs-Applications.md)

*Quality And Delivery*

# Lesson 16 - Debugging And Logging

Bugs are inevitable. This lesson teaches how to pause and inspect running code, how to debug inside VS Code, how to write useful logs, and how to plan for errors.

## 16.1 The built-in inspector

Node.js includes a debugger that works with Chrome DevTools. Start your program with `--inspect`:

```bash
node --inspect app.js          # start and allow a debugger to attach
node --inspect-brk app.js      # also pause on the first line
```

Then open `chrome://inspect` in Chrome and click **inspect** under your Node.js target. You can set breakpoints, step through code, inspect variables, and view the call stack.

You can also place a breakpoint in code with the `debugger` statement. It does nothing unless a debugger is attached.

```js
function total(items) {
  let sum = 0;
  for (const item of items) {
    debugger; // execution pauses here when debugging
    sum += item.price;
  }
  return sum;
}
```

Debugger controls:

- **Continue:** run until the next breakpoint.
- **Step over:** run the current line without entering functions.
- **Step into:** enter the function being called.
- **Step out:** finish the current function.

Note: a basic command-line debugger also exists (`node inspect app.js`), but visual debuggers are easier to use.

---

## 16.2 Debugging in VS Code

VS Code has a debugger built in.

- **Quick start:** open the **JavaScript Debug Terminal** from the terminal dropdown and run `node app.js`. Breakpoints work automatically.
- **Breakpoints:** click in the gutter left of a line number. Right-click a breakpoint to add a **condition** (stop only when `i === 5`) or a **logpoint** (print a message without editing code).
- **Launch configuration:** create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Run app",
      "program": "${workspaceFolder}/app.js",
      "envFile": "${workspaceFolder}/.env"
    }
  ]
}
```

While paused, use these panels:

- **Variables:** see current values.
- **Watch:** track expressions you type.
- **Call Stack:** see how execution arrived here.
- **Debug Console:** run code in the current scope.

Debugging with breakpoints is usually faster than adding many `console.log` calls and removing them later.

---

## 16.3 Structured logging

`console.log` is fine for learning, but real applications need better logs.

Problems with plain text logs: they are hard to search, lack severity levels, and cannot be easily processed by tools.

**Structured logging** writes each log entry as JSON with consistent fields:

```json
{"level":"error","time":"2025-01-15T10:30:00.000Z","msg":"Payment failed","userId":42,"orderId":"A17"}
```

**Log levels** show importance:

| Level | Use for |
|---|---|
| `fatal` | The app cannot continue |
| `error` | An operation failed |
| `warn` | Something unexpected but handled |
| `info` | Normal important events (server started) |
| `debug` | Detailed development info |
| `trace` | Extremely detailed info |

Use a logging library such as **pino** (very fast) or **winston**:

```js
// npm install pino
import pino from "pino";

const logger = pino({ level: process.env.LOG_LEVEL || "info" });

logger.info("Server started");
logger.info({ port: 3000 }, "Listening");
logger.error({ err: new Error("boom"), userId: 42 }, "Request failed");
```

Guidelines:

- Set the level through an environment variable so production can be quiet and development verbose.
- Include context such as request IDs and user IDs, but **never log passwords, tokens, or personal data**.
- Write logs to `stdout` and let the platform collect them.

---

## 16.4 Error handling strategy

Distinguish two kinds of errors:

- **Operational errors:** expected problems at runtime, such as a file not found, a network timeout, or invalid user input. Handle them gracefully.
- **Programmer errors:** bugs, such as reading a property of `undefined`. Fix the code; do not hide them.

Strategy:

1. **Handle errors where you can do something useful** (retry, return a friendly message, use a default). Otherwise let them move up.
2. **Never swallow errors** with an empty `catch {}`.
3. **Use clear custom errors:**

```js
class NotFoundError extends Error {
  constructor(what) {
    super(`${what} not found`);
    this.name = "NotFoundError";
    this.status = 404;
  }
}
```

4. **Keep the original cause** when wrapping: `throw new Error("Could not load config", { cause: err })`.
5. **Add a last-resort handler** that logs and exits, since the process may be in an unknown state:

```js
process.on("uncaughtException", (err) => {
  logger.fatal({ err }, "Uncaught exception");
  process.exit(1);
});

process.on("unhandledRejection", (reason) => {
  logger.fatal({ reason }, "Unhandled rejection");
  process.exit(1);
});
```

6. **Let a process manager restart the app** after a crash (see Lesson 19).
7. **Return safe messages to users.** Show "Something went wrong" instead of stack traces, and log the details privately.

[Previous](./[15]-Building-Command-Line-Tools.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[17]-Testing-Nodejs-Applications.md)