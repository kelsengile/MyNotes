[Previous](./[8]-Paths-OS-And-Process.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[10]-HTTP-And-Networking.md)

*Core APIs*

# Lesson 9 - Buffers And Streams

Files, network connections, and many other sources deliver raw bytes, often in large amounts. Buffers hold bytes in memory, and streams let you process data piece by piece instead of all at once.

## 9.1 Binary data and Buffers

A **Buffer** is a fixed-size chunk of memory that holds raw bytes. Each byte is a number from 0 to 255. Buffers are used when working with files, images, network packets, and encodings.

```js
const buf1 = Buffer.from("Hello", "utf8");
console.log(buf1);               // <Buffer 48 65 6c 6c 6f>
console.log(buf1.length);        // 5 (bytes)
console.log(buf1.toString());    // "Hello"
console.log(buf1.toString("hex"));    // "48656c6c6f"
console.log(buf1.toString("base64")); // "SGVsbG8="

const buf2 = Buffer.alloc(4);    // 4 zero-filled bytes
buf2[0] = 255;

const joined = Buffer.concat([buf1, buf2]);
```

Important facts:

- `buffer.length` counts **bytes**, not characters. The text `"é"` is 2 bytes in UTF-8.
- When you call `fs.readFile` without an encoding, you get a Buffer.
- Use `Buffer.alloc(size)` for safe, zeroed memory. Avoid the old `new Buffer()` constructor.
- Buffers are `Uint8Array`s, so array-like operations work.

---

## 9.2 Readable and writable streams

A **stream** processes data in small **chunks** as it arrives. This uses little memory and starts working immediately, even for huge files.

Four stream types:

| Type | Purpose | Example |
|---|---|---|
| Readable | Source of data | `fs.createReadStream`, HTTP request |
| Writable | Destination for data | `fs.createWriteStream`, HTTP response |
| Duplex | Both readable and writable | TCP socket |
| Transform | Duplex that changes data | `zlib.createGzip()` |

Reading a large file in chunks:

```js
const fs = require("node:fs");

const readable = fs.createReadStream("big.txt", { encoding: "utf8" });

readable.on("data", (chunk) => console.log("Got", chunk.length, "characters"));
readable.on("end", () => console.log("No more data"));
readable.on("error", (err) => console.error(err));
```

Writing:

```js
const writable = fs.createWriteStream("out.txt");
writable.write("line 1\n");
writable.write("line 2\n");
writable.end("last line\n"); // closes the stream
writable.on("finish", () => console.log("Written"));
```

Readable streams can also be consumed with `for await`:

```js
for await (const chunk of fs.createReadStream("big.txt")) {
  console.log(chunk.length);
}
```

---

## 9.3 Piping and backpressure

**Piping** connects a readable stream to a writable stream so data flows automatically.

```js
const fs = require("node:fs");
const { pipeline } = require("node:stream/promises");

await pipeline(
  fs.createReadStream("input.txt"),
  fs.createWriteStream("copy.txt")
);
console.log("Copied");
```

Use `pipeline` instead of `.pipe()`. It forwards errors and cleans up all streams if any one fails.

**Backpressure** happens when a source produces data faster than the destination can handle it. Without control, memory would fill up. Streams handle this automatically:

- `writable.write()` returns `false` when its internal buffer is full.
- The source should pause until the writable emits `"drain"`.
- `pipe` and `pipeline` do this for you.

If you write streams by hand with `data` events, you must respect `write()`'s return value yourself.

---

## 9.4 Transform streams

A **Transform** stream reads data, changes it, and outputs the result. It sits in the middle of a pipeline.

Built-in example: compressing a file.

```js
const fs = require("node:fs");
const zlib = require("node:zlib");
const { pipeline } = require("node:stream/promises");

await pipeline(
  fs.createReadStream("input.txt"),
  zlib.createGzip(),
  fs.createWriteStream("input.txt.gz")
);
```

Custom transform that uppercases text:

```js
const { Transform } = require("node:stream");

const upper = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  },
});

process.stdin.pipe(upper).pipe(process.stdout);
```

Calling `callback(error)` signals a failure; `callback(null, data)` passes data downstream. Because each step handles one chunk at a time, you can chain many transforms (decompress, parse, filter, compress) without ever loading the whole file into memory.

[Previous](./[8]-Paths-OS-And-Process.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[10]-HTTP-And-Networking.md)