[Previous](./[2]-JavaScript-For-Node.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[4]-The-Event-Loop.md)

*Basics*

# Lesson 3 - Modules And npm

Real programs are built from many small pieces. This lesson shows how to use Node.js's built-in modules, install packages from npm, write your own modules, and publish them.

## 3.1 Using built-in modules

Node.js ships with modules that need no installation. Import them with the `node:` prefix, which makes it clear they are built in.

```js
// CommonJS
const os = require("node:os");
// ESM
import path from "node:path";

console.log(os.platform());
console.log(path.join("a", "b", "c.txt"));
```

Commonly used built-in modules:

| Module | Purpose |
|---|---|
| `fs` | Read and write files |
| `path` | Work with file paths |
| `os` | Operating system information |
| `http` / `https` | Servers and requests |
| `events` | Event emitters |
| `stream` | Streaming data |
| `crypto` | Hashing and encryption |
| `util` | Helper utilities |
| `child_process` | Run other programs |

---

## 3.2 Installing packages

**npm** (Node Package Manager) comes with Node.js and connects you to the npm registry, a huge collection of open-source packages.

```bash
npm init -y              # create package.json
npm install lodash       # install and add to dependencies
npm install -D eslint    # install as a development dependency
npm uninstall lodash     # remove a package
```

Installed packages live in the `node_modules` folder. Do not edit it by hand, and do not commit it to Git (add it to `.gitignore`).

```js
const _ = require("lodash");
console.log(_.chunk([1, 2, 3, 4], 2)); // [[1, 2], [3, 4]]
```

Dependency types:

- **dependencies:** needed when the program runs.
- **devDependencies:** needed only while developing (tests, linters, build tools).

You can also run a package without installing it permanently: `npx cowsay hello`.

---

## 3.3 Creating your own modules

Split your code into files so each file has one clear job.

```js
// utils/greet.js (CommonJS)
function greet(name) {
  return `Hello, ${name}!`;
}
module.exports = { greet };
```

```js
// index.js
const { greet } = require("./utils/greet");
console.log(greet("Maria"));
```

The ESM version:

```js
// utils/greet.js
export function greet(name) {
  return `Hello, ${name}!`;
}
```

```js
// index.js
import { greet } from "./utils/greet.js";
```

Tips:

- Use `./` or `../` for your own files. A name without a path (like `lodash`) is looked up in `node_modules` or the built-ins.
- A folder with an `index.js` can be imported by its folder name.
- Export only what other files need.

---

## 3.4 Publishing basics

You can share a package on npm so others can install it.

1. Create an account at npmjs.com and log in with `npm login`.
2. Make sure `package.json` has a unique `name`, a `version`, a `main` (entry file), and a `description`.
3. Use the `files` field or a `.npmignore` file to control what is uploaded.
4. Preview what will be published with `npm pack --dry-run`.
5. Publish with `npm publish`.

```json
{
  "name": "my-unique-package-name",
  "version": "1.0.0",
  "description": "A small example package",
  "main": "index.js",
  "files": ["index.js"],
  "license": "MIT"
}
```

When you release changes, update the version with `npm version patch` (or `minor` or `major`) and publish again. A published version number cannot be reused.

[Previous](./[2]-JavaScript-For-Node.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[4]-The-Event-Loop.md)