[Previous](./[10]-HTTP-And-Networking.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[12]-Package-Management.md)

*Core APIs*

# Lesson 11 - Child Processes And Worker Threads

Node.js runs your JavaScript on one thread, but your computer has many CPU cores. This lesson shows how to run other programs, use multiple cores, and offload heavy work.

## 11.1 Spawning processes

The `child_process` module lets Node.js run other programs, such as shell commands, scripts, or other Node.js files.

```js
const { exec, execFile, spawn, fork } = require("node:child_process");
```

| Function | Best for |
|---|---|
| `exec` | Short shell commands; returns all output at the end |
| `execFile` | Running a program directly, without a shell (safer) |
| `spawn` | Long-running programs; output arrives as a stream |
| `fork` | Running another Node.js script with a message channel |

```js
// exec: output is buffered
exec("node --version", (err, stdout, stderr) => {
  if (err) return console.error(err);
  console.log(stdout.trim());
});

// spawn: streaming output
const child = spawn("ls", ["-la"]);
child.stdout.on("data", (d) => process.stdout.write(d));
child.on("close", (code) => console.log("Exited with", code));
```

`fork` also gives you messaging between parent and child:

```js
// parent.js
const { fork } = require("node:child_process");
const child = fork("./child.js");
child.send({ task: "hello" });
child.on("message", (m) => console.log("Parent got:", m));

// child.js
process.on("message", (m) => process.send({ reply: m.task + " back" }));
```

Security warning: never put unchecked user input into `exec` strings. It allows command injection. Prefer `execFile` or `spawn` with an argument array.

---

## 11.2 The cluster module

A single Node.js process uses only one CPU core. The **cluster** module starts several copies of your server (workers) that share one port, so you can use all cores.

```js
const cluster = require("node:cluster");
const http = require("node:http");
const os = require("node:os");

if (cluster.isPrimary) {
  const count = os.cpus().length;
  for (let i = 0; i < count; i++) cluster.fork();

  cluster.on("exit", (worker) => {
    console.log(`Worker ${worker.process.pid} died, starting a new one`);
    cluster.fork();
  });
} else {
  http.createServer((req, res) => {
    res.end(`Handled by process ${process.pid}`);
  }).listen(3000);
}
```

Each worker is a separate process with its own memory, so they cannot share variables. Store shared state in a database or cache. In production, many teams use a process manager or container orchestrator to run multiple instances instead of writing cluster code.

---

## 11.3 Worker threads

**Worker threads** run JavaScript in parallel threads inside the same process. They are meant for CPU-heavy tasks (image processing, hashing, large calculations) that would otherwise block the event loop.

```js
// main.js
const { Worker } = require("node:worker_threads");

function runWorker(n) {
  return new Promise((resolve, reject) => {
    const worker = new Worker("./worker.js", { workerData: n });
    worker.on("message", resolve);
    worker.on("error", reject);
  });
}

runWorker(40).then((result) => console.log("Result:", result));
console.log("Main thread stays responsive");
```

```js
// worker.js
const { parentPort, workerData } = require("node:worker_threads");

function fib(n) { return n < 2 ? n : fib(n - 1) + fib(n - 2); }

parentPort.postMessage(fib(workerData));
```

Key points:

- Workers communicate with the main thread by **messages** (`postMessage`).
- Data is copied between threads. `SharedArrayBuffer` allows real shared memory.
- Starting a worker has a cost, so for many tasks use a pool (for example the `piscina` library).
- Worker threads do not help with I/O work, which Node.js already handles asynchronously.

---

## 11.4 Choosing a concurrency model

| Need | Use |
|---|---|
| Many network or file operations | Plain async code (the event loop) |
| Run an external program or command | `child_process` (`spawn` or `execFile`) |
| Use all CPU cores for a server | Multiple instances (`cluster` or process manager) |
| CPU-heavy calculation in one app | Worker threads |
| Isolation, so a crash cannot affect others | Child processes |

A good rule: start with plain async code. Only move to workers or multiple processes when you measure a real problem such as high CPU use or a blocked event loop.

[Previous](./[10]-HTTP-And-Networking.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[12]-Package-Management.md)