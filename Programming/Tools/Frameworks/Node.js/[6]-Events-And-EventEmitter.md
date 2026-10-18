[Previous](./[5]-Asynchronous-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[7]-The-File-System.md)

*Asynchronous Node*

# Lesson 6 - Events And EventEmitter

Many Node.js objects (servers, streams, processes) announce things that happen by emitting events. This lesson teaches the pattern behind them and how to use it yourself.

## 6.1 The observer pattern

In the **observer pattern**, one object (the subject) announces that something happened, and any number of other pieces of code (observers) react to it. The subject does not need to know who is listening.

Examples in Node.js:

- An HTTP server emits `request` when a client connects.
- A stream emits `data` when a chunk arrives.
- A process emits `exit` when it is about to close.

This keeps code loosely coupled: you can add new reactions without changing the code that triggers the event.

---

## 6.2 Emitting and listening

The `events` module provides the `EventEmitter` class.

```js
const EventEmitter = require("node:events");

const emitter = new EventEmitter();

// Listen
emitter.on("greet", (name) => {
  console.log(`Hello, ${name}!`);
});

// Emit
emitter.emit("greet", "Ana"); // Hello, Ana!
```

Main methods:

| Method | Purpose |
|---|---|
| `on(event, fn)` | Add a listener |
| `once(event, fn)` | Add a listener that runs only once |
| `off(event, fn)` | Remove a listener |
| `emit(event, ...args)` | Trigger the event with arguments |
| `listenerCount(event)` | Count listeners |

Details:

- Listeners run **synchronously**, in the order they were added.
- `emit` returns `true` if there were listeners, otherwise `false`.
- To remove a listener, keep a reference to the function you added.

```js
function onMsg(m) { console.log(m); }
emitter.on("msg", onMsg);
emitter.off("msg", onMsg);
```

---

## 6.3 Once and error events

**`once`** runs a listener a single time and then removes it. This is useful for one-time setup events.

```js
emitter.once("ready", () => console.log("Ready!"));
emitter.emit("ready"); // Ready!
emitter.emit("ready"); // nothing
```

You can also wait for an event as a promise:

```js
const { once } = require("node:events");

async function main() {
  const [value] = await once(emitter, "done");
  console.log(value);
}
setTimeout(() => emitter.emit("done", 42), 100);
main();
```

**The `error` event is special.** If an emitter emits `"error"` and no listener is registered, Node.js throws the error and the process crashes.

```js
emitter.on("error", (err) => {
  console.error("Handled:", err.message);
});
emitter.emit("error", new Error("Something broke"));
```

Always add an `error` listener on emitters that can fail, such as streams, sockets, and servers.

Another warning: adding more than 10 listeners for one event prints a possible memory leak warning. Remove listeners you no longer need.

---

## 6.4 Building event-driven code

Extend `EventEmitter` to build your own event-driven classes.

```js
const EventEmitter = require("node:events");

class Downloader extends EventEmitter {
  start(files) {
    this.emit("start", files.length);
    files.forEach((file, i) => {
      // pretend work happens here
      this.emit("progress", file, i + 1);
    });
    this.emit("done");
  }
}

const d = new Downloader();
d.on("start", (n) => console.log(`Downloading ${n} files`));
d.on("progress", (file, i) => console.log(`Got ${file} (${i})`));
d.on("done", () => console.log("All finished"));

d.start(["a.zip", "b.zip"]);
```

Design tips:

- Use clear, consistent event names such as `"connected"`, `"data"`, `"close"`.
- Document which arguments each event passes.
- Emit `error` for failures instead of throwing from inside asynchronous work.
- Prefer events when many parts need to react; prefer a plain function call or promise when there is just one result.

[Previous](./[5]-Asynchronous-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[7]-The-File-System.md)