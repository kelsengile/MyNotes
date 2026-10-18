[Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[2]-JavaScript-For-Node.md)

*Basics*

# Lesson 1 - What Is Node.js

Node.js lets you run JavaScript outside the browser. This lesson explains what that means, what is inside Node.js, how to install it, and how to run your first script.

## 1.1 Runtime vs browser

A **runtime** is the environment that executes your code and gives it access to features. JavaScript itself only defines the language (variables, functions, objects). Everything else comes from the environment it runs in.

- **Browser:** provides `window`, `document`, `fetch`, `localStorage`, and the DOM so you can manipulate web pages.
- **Node.js:** provides the file system, networking, processes, and operating system access so you can build servers and tools.

Node.js has no `document` or `window`, because there is no web page. Browsers cannot read arbitrary files from your disk, while Node.js can.

```js
// Works in Node.js
console.log("Hello from Node.js");

// Fails in Node.js: document is not defined
// document.getElementById("app");
```

---

## 1.2 V8 and libuv

Node.js is built from several parts:

- **V8:** the JavaScript engine created by Google for Chrome. It compiles JavaScript into fast machine code.
- **libuv:** a C library that handles the event loop, the thread pool, and asynchronous operations such as file and network I/O.
- **Node.js core (C++ and JavaScript):** the glue that exposes built-in modules like `fs`, `http`, and `path` to your code.

Your JavaScript runs in V8. When you ask Node.js to read a file, the request is handed to libuv, which does the work in the background and notifies your code when it is finished. This is why Node.js can handle many tasks without freezing.

---

## 1.3 Installing Node.js and version managers

Download the installer from [nodejs.org/en/download](https://nodejs.org/en/download). Node.js has two kinds of releases:

- **LTS (Long Term Support):** stable, receives updates for years. Use this for learning and production.
- **Current:** has the newest features but a shorter support period.

Check your install:

```bash
node --version
npm --version
```

A **version manager** lets you install and switch between several Node.js versions, which is useful when different projects need different versions.

| Tool | Platform | Example |
|---|---|---|
| nvm | macOS, Linux | `nvm install --lts` |
| nvm-windows | Windows | `nvm install lts` |
| fnm | All | `fnm install --lts` |
| Volta | All | `volta install node` |

```bash
nvm install --lts
nvm use --lts
nvm ls
```

---

## 1.4 Running scripts and the REPL

Create a file named `hello.js`:

```js
const name = "Node";
console.log(`Hello, ${name}!`);
```

Run it from the terminal:

```bash
node hello.js
```

The **REPL** (Read-Eval-Print Loop) is an interactive prompt for quick experiments. Start it by typing `node` with no file:

```bash
node
> 2 + 3
5
> const greet = (n) => "Hi " + n
> greet("Ana")
'Hi Ana'
> .exit
```

Useful REPL commands:

- `.help` lists all commands.
- `.editor` opens multi-line editing mode.
- `.save file.js` saves your session to a file.
- `.exit` or pressing `Ctrl+D` quits.

You can also run a quick expression without a file using `node -e "console.log(1 + 1)"`. During development, `node --watch app.js` restarts your script automatically whenever the file changes.

[Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[2]-JavaScript-For-Node.md)