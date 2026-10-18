[Previous](./[6]-Events-And-EventEmitter.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[8]-Paths-OS-And-Process.md)

*Core APIs*

# Lesson 7 - The File System

The `fs` module lets Node.js read, write, and manage files and folders. It is one of the most used built-in modules.

## 7.1 Reading and writing files

The recommended style is the promises API with `async/await`.

```js
const fs = require("node:fs/promises");

async function main() {
  // Write (creates or overwrites)
  await fs.writeFile("hello.txt", "Hello, file!\n");

  // Append
  await fs.appendFile("hello.txt", "Another line\n");

  // Read (pass an encoding to get a string, otherwise you get a Buffer)
  const text = await fs.readFile("hello.txt", "utf8");
  console.log(text);

  // Copy, rename, delete
  await fs.copyFile("hello.txt", "copy.txt");
  await fs.rename("copy.txt", "renamed.txt");
  await fs.unlink("renamed.txt");
}

main().catch(console.error);
```

Notes:

- Relative paths are relative to where you **run** `node`, not where the script file is. Build paths from `__dirname` when you need the script's folder.
- `readFile` loads the whole file into memory. For very large files use streams (Lesson 9).
- Common error codes: `ENOENT` (not found), `EACCES` (permission denied), `EEXIST` (already exists).

---

## 7.2 Directories and metadata

```js
const fs = require("node:fs/promises");

async function main() {
  // Create folders (recursive creates parents too)
  await fs.mkdir("data/logs", { recursive: true });

  // List contents
  const names = await fs.readdir("data");
  const entries = await fs.readdir("data", { withFileTypes: true });
  for (const e of entries) {
    console.log(e.name, e.isDirectory() ? "folder" : "file");
  }

  // File information
  const info = await fs.stat("hello.txt");
  console.log(info.size, info.mtime, info.isFile());

  // Check existence by trying to access
  try {
    await fs.access("hello.txt");
    console.log("exists");
  } catch {
    console.log("missing");
  }

  // Remove a folder and everything in it
  await fs.rm("data", { recursive: true, force: true });
}
main();
```

`stat` returns size in bytes, modification time (`mtime`), and type checks like `isFile()` and `isDirectory()`.

---

## 7.3 Sync vs async vs promises APIs

The `fs` module offers three styles for most operations:

| Style | Example | Use when |
|---|---|---|
| Synchronous | `fs.readFileSync(p, "utf8")` | Startup code, small scripts |
| Callback | `fs.readFile(p, "utf8", cb)` | Older code |
| Promises | `await fsp.readFile(p, "utf8")` | Most modern code |

```js
const fs = require("node:fs");
const fsp = require("node:fs/promises");

const a = fs.readFileSync("a.txt", "utf8");          // blocks
fs.readFile("a.txt", "utf8", (err, d) => {});        // callback
const b = await fsp.readFile("a.txt", "utf8");       // promise
```

Synchronous calls block the event loop, so avoid them inside servers handling requests. Reading a config file once at startup with a sync call is perfectly fine.

---

## 7.4 Watching files

Node.js can notify you when files change.

```js
const fs = require("node:fs");

const watcher = fs.watch("./src", { recursive: true }, (eventType, filename) => {
  console.log(eventType, filename); // "change" or "rename"
});

// Stop watching later
// watcher.close();
```

Things to know:

- `fs.watch` relies on the operating system, so behavior differs slightly across platforms, and events can fire more than once for a single save. Debounce if needed.
- The `filename` argument may be missing on some platforms.
- For production file watching, libraries like `chokidar` provide more consistent behavior.
- To restart a program when its files change, use the built-in `node --watch app.js`.

[Previous](./[6]-Events-And-EventEmitter.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[8]-Paths-OS-And-Process.md)