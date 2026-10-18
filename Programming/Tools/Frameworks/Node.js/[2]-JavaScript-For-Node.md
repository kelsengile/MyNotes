[Previous](./[1]-What-Is-Nodejs.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[3]-Modules-And-npm.md)

*Basics*

# Lesson 2 - JavaScript For Node

You already know JavaScript from the browser or from other lessons. This lesson covers what is different or special about JavaScript when it runs in Node.js.

## 2.1 Globals and the process object

Node.js provides a few global values available in every file without importing:

- `globalThis`: the global object (like `window` in browsers).
- `console`: logging.
- `setTimeout`, `setInterval`, `setImmediate`: timers.
- `process`: information and control over the running program.
- `Buffer`, `URL`, `fetch`, `AbortController`, `structuredClone`: common utilities.

The `process` object is especially important:

```js
console.log(process.version);   // Node.js version, e.g. "v22.11.0"
console.log(process.platform);  // "win32", "linux", "darwin"
console.log(process.cwd());     // current working directory
console.log(process.argv);      // command-line arguments
console.log(process.env.HOME);  // an environment variable
```

Note that `var` at the top level of a file does not create a global in Node.js, because each file is its own module.

---

## 2.2 CommonJS modules

**CommonJS (CJS)** is Node.js's original module system. It uses `require` to import and `module.exports` to export.

```js
// math.js
function add(a, b) {
  return a + b;
}
module.exports = { add };
```

```js
// app.js
const { add } = require("./math");
console.log(add(2, 3)); // 5
```

Key facts:

- `require()` is synchronous and runs at the moment it is called.
- Modules are cached: requiring the same file twice returns the same object.
- Each file gets helper variables: `__filename`, `__dirname`, `module`, `exports`, and `require`.
- Files ending in `.js` are treated as CommonJS by default, unless configured otherwise.

---

## 2.3 ES modules

**ES modules (ESM)** are the official JavaScript standard, using `import` and `export`.

```js
// math.mjs
export function add(a, b) {
  return a + b;
}
export default function multiply(a, b) {
  return a * b;
}
```

```js
// app.mjs
import multiply, { add } from "./math.mjs";
console.log(add(2, 3), multiply(2, 3));
```

Node.js treats a file as ESM when:

- it ends in `.mjs`, or
- the nearest `package.json` contains `"type": "module"`.

Differences from CommonJS:

- File extensions are required in relative imports (`./math.mjs`, not `./math`).
- `__dirname` and `__filename` do not exist. Use `import.meta.dirname` and `import.meta.filename` in modern Node.js, or `import.meta.url`.
- Top-level `await` is allowed.
- Built-in modules can be imported with the `node:` prefix: `import fs from "node:fs";`.

```js
// Top-level await in ESM
const res = await fetch("https://example.com");
console.log(res.status);
```

---

## 2.4 Differences from browser JavaScript

| Topic | Browser | Node.js |
|---|---|---|
| Global object | `window` | `globalThis` / `global` |
| DOM | Yes | No |
| File access | Restricted | Full access with `fs` |
| Modules | ESM | CommonJS and ESM |
| Environment | Many browsers and versions | You control the Node.js version |
| Security | Sandboxed | Runs with your user's permissions |
| Entry point | HTML `<script>` tags | `node file.js` |

Because Node.js has no sandbox, a script can delete files or open network connections. Only run code you trust.

Language features are the same (classes, `async/await`, destructuring, optional chaining), but you do not need to worry about browser compatibility. You only need to check that your Node.js version supports a feature.

[Previous](./[1]-What-Is-Nodejs.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[3]-Modules-And-npm.md)