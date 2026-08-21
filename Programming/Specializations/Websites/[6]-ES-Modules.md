[Previous](./[5]-Fetch-API.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[7]-Error-Handling-and-Debugging.md)

*Intermediate JavaScript*

# Lesson 6 - ES Modules & Code Organization

## 6.1 Why Split Code into Modules

Once a project grows past a few hundred lines, keeping everything in one file makes it hard to navigate, test, and reuse. **Modules** let you split code into separate files, each responsible for one thing, and explicitly share functionality between them instead of relying on global variables.

---

## 6.2 Exporting and Importing

**ES Modules (ESM)** are the standard JavaScript module system. A file exports what it wants to share:

```js
// math.js
export function add(a, b) {
  return a + b;
}
export const PI = 3.14159;
```

And another file imports only what it needs:

```js
// main.js
import { add, PI } from "./math.js";
console.log(add(2, 3));
```

---

## 6.3 Default Exports

A module can also have one **default export**, useful when a file's main purpose is a single thing (like a component):

```js
// Button.js
export default function Button() { /* ... */ }

// main.js
import Button from "./Button.js";
```

Default imports can be named anything on import, since there's no export name to match.

---

## 6.4 Using Modules in the Browser

To use ESM directly in the browser (without a bundler), add `type="module"` to your script tag:

```html
<script type="module" src="./main.js"></script>
```

Module scripts are deferred automatically, run in strict mode, and each file's variables stay scoped to that module rather than leaking into the global `window` object.

---

## 6.5 CommonJS, for Context

Node.js historically used **CommonJS** (`require()` / `module.exports`) instead of ESM. You'll still see it in older Node code and some npm packages:

```js
// CommonJS
const math = require("./math.js");
module.exports = { add };
```

Modern Node.js supports both, and modern build tools (Lesson 13) can convert between them, but ESM is the direction the ecosystem has settled on.

---

[Previous](./[5]-Fetch-API.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[7]-Error-Handling-and-Debugging.md)
