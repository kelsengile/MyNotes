[Previous](./[17]-Testing-Nodejs-Applications.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[19]-Deployment.md)

*Quality And Delivery*

# Lesson 18 - Security And Performance

A working application should also be safe and fast. This lesson covers checking dependencies, avoiding common vulnerabilities, finding performance problems, and using caching.

## 18.1 Dependency auditing

Your app includes hundreds of packages written by strangers, and any of them can have security flaws. This is called **supply chain risk**.

```bash
npm audit               # list known vulnerabilities
npm audit fix           # apply safe automatic fixes
npm outdated            # find old packages
```

Habits that reduce risk:

- Install fewer dependencies. Do you really need a package for one small function?
- Check a package's popularity, maintenance, and last update before installing.
- Commit your lock file and use `npm ci` in automated builds.
- Enable automatic update tools such as **Dependabot** or **Renovate**.
- Beware of **typosquatting**, where a malicious package has a name almost identical to a popular one (`lodahs` instead of `lodash`).
- Remove unused packages with `npm uninstall`.
- Consider `npm install --ignore-scripts` for untrusted packages, since install scripts run code on your machine.

---

## 18.2 Common vulnerabilities

Never trust data coming from users, including query strings, bodies, headers, and files.

**Injection:** user input is treated as code.

- SQL injection: use parameterized queries (Lesson 14).
- Command injection: avoid `exec` with user input; use `execFile` with an argument array (Lesson 11).
- Avoid `eval()` and `new Function()` on any external data.

**Cross-site scripting (XSS):** attacker scripts run in other users' browsers. Escape output when building HTML and use templating tools that escape by default.

**Path traversal:** input like `../../etc/passwd` escapes the intended folder.

```js
const path = require("node:path");
const base = path.resolve("public");
const target = path.resolve(base, userInput);

if (!target.startsWith(base + path.sep)) {
  throw new Error("Invalid path");
}
```

**Broken authentication:** hash passwords with a slow algorithm (`bcrypt`, `argon2`, or `crypto.scrypt`), never store them as plain text, and use secure session or token handling.

**Other essentials:**

- Use HTTPS everywhere in production.
- Validate input with a schema library such as `zod` or `joi`.
- Set security headers (the `helmet` package does this for Express).
- Add **rate limiting** to prevent abuse and brute-force attacks.
- Limit request body sizes to avoid memory exhaustion.
- Keep secrets out of code and logs (Lesson 13).
- Run with the lowest permissions needed, and never as the root user in production.
- Keep Node.js updated to a supported LTS version.

---

## 18.3 Profiling and memory leaks

Measure before optimizing. Guessing often wastes time.

**CPU profiling:**

```bash
node --prof app.js
node --prof-process isolate-*.log > profile.txt
```

Or use `node --inspect` and the **Performance** and **Profiler** tabs in Chrome DevTools to see which functions take the most time. Tools like `clinic` and `0x` produce visual reports (flame graphs).

**Measuring code:**

```js
console.time("task");
doWork();
console.timeEnd("task");

const start = performance.now();
doWork();
console.log(`${performance.now() - start} ms`);
```

**Event loop health:** if the event loop is blocked, every request slows down. Avoid long synchronous work (Lesson 4).

**Memory leaks:** memory that grows forever and is never released. Common causes:

- Global arrays or maps that keep growing (unbounded caches).
- Event listeners added repeatedly and never removed.
- Timers (`setInterval`) that are never cleared.
- Closures that keep large objects alive.

Watch memory with:

```js
setInterval(() => {
  const mb = process.memoryUsage().heapUsed / 1024 / 1024;
  console.log(`Heap: ${mb.toFixed(1)} MB`);
}, 5000);
```

To investigate, run with `--inspect`, take **heap snapshots** in Chrome DevTools at different times, and compare them to see which objects keep growing. For large data, use streams instead of loading everything into memory (Lesson 9).

---

## 18.4 Caching basics

**Caching** means keeping the result of expensive work so you do not repeat it. Typical targets: database queries, API calls, and computed values.

Simple in-memory cache with an expiry time:

```js
const cache = new Map();

async function cached(key, ttlMs, loader) {
  const hit = cache.get(key);
  if (hit && hit.expires > Date.now()) return hit.value;

  const value = await loader();
  cache.set(key, { value, expires: Date.now() + ttlMs });
  return value;
}

// Usage
const user = await cached("user:1", 60_000, () => db.getUser(1));
```

Caching layers:

| Layer | Example |
|---|---|
| In-process memory | A `Map` or the `lru-cache` package |
| Shared cache | Redis or Memcached, shared by all server instances |
| HTTP caching | `Cache-Control` and `ETag` headers |
| CDN | Caches static files near users |

Things to remember:

- Always set a **time to live (TTL)** or a size limit, or the cache becomes a memory leak.
- **Invalidation** (removing stale data when the source changes) is the hard part.
- In-process caches are separate per instance. Use a shared cache when running multiple instances (Lesson 11).
- Cache only data that is safe to reuse and does not differ between users unless the key includes the user.

Other quick wins: use `async` I/O, run heavy tasks in worker threads, enable compression, use connection pools (Lesson 14), and add database indexes.

[Previous](./[17]-Testing-Nodejs-Applications.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[19]-Deployment.md)