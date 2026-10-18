[Previous](./[7]-The-File-System.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[9]-Buffers-And-Streams.md)

*Core APIs*

# Lesson 8 - Paths, OS, And Process

This lesson covers three modules and objects that help your program understand where it is running and how it was started: `path`, `os`, and `process`.

## 8.1 The path module

Paths look different on Windows (`C:\Users\me\file.txt`) and Linux/macOS (`/home/me/file.txt`). The `path` module builds and reads paths correctly on any system, so never join paths by hand with `+` or `"/"`.

```js
const path = require("node:path");

path.join("data", "logs", "app.log");     // data/logs/app.log
path.resolve("data", "app.log");          // absolute path from the current folder
path.basename("/a/b/file.txt");           // "file.txt"
path.basename("/a/b/file.txt", ".txt");   // "file"
path.dirname("/a/b/file.txt");            // "/a/b"
path.extname("/a/b/file.txt");            // ".txt"
path.parse("/a/b/file.txt");              // { root, dir, base, ext, name }
path.isAbsolute("/a/b");                  // true
path.relative("/a/b", "/a/c/d");          // "../c/d"
```

A very common pattern is building a path relative to the current file:

```js
const configPath = path.join(__dirname, "config", "settings.json");
```

---

## 8.2 The os module

The `os` module gives information about the computer.

```js
const os = require("node:os");

console.log(os.platform());   // "linux", "darwin", "win32"
console.log(os.arch());       // "x64", "arm64"
console.log(os.cpus().length);// number of logical CPU cores
console.log(os.totalmem());   // total memory in bytes
console.log(os.freemem());    // free memory in bytes
console.log(os.homedir());    // user's home folder
console.log(os.tmpdir());     // temp folder
console.log(os.hostname());   // computer name
console.log(os.EOL);          // line ending for this OS
```

Typical uses: choosing how many worker processes to start (`os.cpus().length`), finding a safe temp folder, and printing diagnostics.

---

## 8.3 Command-line arguments

`process.argv` is an array of everything typed on the command line.

```bash
node app.js hello --name=Ana --verbose
```

```js
console.log(process.argv);
// [
//   "/path/to/node",       index 0: Node.js executable
//   "/path/to/app.js",     index 1: your script
//   "hello",               index 2 onward: your arguments
//   "--name=Ana",
//   "--verbose"
// ]

const args = process.argv.slice(2);
console.log(args); // ["hello", "--name=Ana", "--verbose"]
```

For simple parsing, Node.js includes `util.parseArgs`:

```js
const { parseArgs } = require("node:util");

const { values, positionals } = parseArgs({
  options: {
    name: { type: "string" },
    verbose: { type: "boolean", short: "v" },
  },
  allowPositionals: true,
});

console.log(values.name, values.verbose, positionals);
```

All argument values arrive as strings, so convert numbers yourself with `Number()`.

---

## 8.4 Exit codes and signals

When a program ends, it returns an **exit code** to the shell. `0` means success; any other number means failure. Scripts and CI tools rely on this.

```js
if (!process.argv[2]) {
  console.error("Please provide a name");
  process.exit(1); // failure
}
console.log("OK"); // process ends naturally with code 0
```

Prefer setting `process.exitCode = 1` and letting the program finish, because `process.exit()` ends immediately and may cut off pending output.

Check the last exit code in the shell with `echo $?` (Linux/macOS) or `echo %ERRORLEVEL%` (Windows cmd).

**Signals** are messages the operating system sends to a process. The common ones:

- `SIGINT`: sent when you press `Ctrl+C`.
- `SIGTERM`: a polite request to stop, used by Docker and process managers.

Handle them to shut down gracefully:

```js
process.on("SIGINT", () => {
  console.log("Cleaning up...");
  // close servers, database connections, files
  process.exit(0);
});

process.on("exit", (code) => {
  console.log("Exiting with code", code); // only synchronous work here
});
```

`SIGKILL` cannot be caught; it stops the process immediately.

[Previous](./[7]-The-File-System.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[9]-Buffers-And-Streams.md)