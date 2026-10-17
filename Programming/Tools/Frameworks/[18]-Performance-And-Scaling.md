[Previous](./[17]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[19]-Choosing-A-Framework.md)

*Quality And Delivery*

# Lesson 18 - Performance And Scaling

A working application is not necessarily a *fast* one, and a fast application with ten users may crumble with ten thousand. This lesson covers how performance is shaped by where pages are rendered, how caching and lazy loading avoid repeated work, how to shrink what you send to users, and how to grow an application to serve more of them.

A rule to keep in mind throughout: **measure first, optimize second.** Guessing at bottlenecks wastes time and often makes code more complicated for no benefit. Optimize what measurements show is actually slow.

## 18.1 Rendering Strategies (CSR, SSR, SSG)

"Rendering" here means **turning your components and data into the HTML the user sees**, and, crucially, *where and when* that happens. Modern frameworks, especially meta-frameworks like Next.js, let you choose per project or even per page.

### Client-Side Rendering (CSR)

The server sends an almost empty HTML page and a JavaScript bundle. The **browser** runs the JavaScript, fetches data, and builds the page.

```text
Browser ◄── nearly empty HTML + JS ── Server
Browser: runs JS → fetches data → builds the page
```

- **Pros:** very interactive after loading; simple, cheap static hosting; smooth navigation.
- **Cons:** the user may see a blank screen or spinner at first; slower first load on weak devices; harder for search engines and link previews to read content.

### Server-Side Rendering (SSR)

The **server** builds the complete HTML for each request and sends it ready to display. The browser then loads JavaScript to make it interactive.

```text
Browser ── request ──► Server builds full HTML ──► Browser shows content immediately
```

- **Pros:** content appears quickly; works well for search engines and link previews; data is always fresh.
- **Cons:** the server does work on every request, so it needs more server capacity; the response cannot start until the server finishes.

### Static Site Generation (SSG)

The HTML for each page is built **once, ahead of time, at build time**, and saved as files that can be served from a CDN.

```text
Build time:  data + templates → HTML files
Request time: CDN returns the ready-made file
```

- **Pros:** extremely fast and cheap to serve; very scalable and secure.
- **Cons:** content is only as fresh as the last build; unsuitable for highly personalized or constantly changing pages unless combined with other techniques.

### Hydration and incremental regeneration

Two terms often accompany these strategies:

- **Hydration**: when server-rendered or pre-built HTML arrives, the browser loads the framework's JavaScript, which "attaches" interactivity to the existing HTML rather than rebuilding it.
- **Incremental Static Regeneration (ISR)**: a hybrid in which statically generated pages are rebuilt in the background after a set time, combining static speed with reasonably fresh content.

### Comparing the strategies

| | CSR | SSR | SSG |
|---|---|---|---|
| **HTML is built...** | In the browser | On the server, per request | At build time |
| **First content appears** | After JavaScript runs | Quickly | Very quickly |
| **Search-engine friendly** | Needs care | Yes | Yes |
| **Content freshness** | Always current (fetched live) | Always current | As of the last build |
| **Server load** | Low | Higher | Very low |
| **Good for** | Dashboards and logged-in apps | Dynamic, SEO-sensitive pages | Blogs, docs, marketing sites |

Many real applications **mix** them: static marketing pages, server-rendered product pages, and a client-rendered dashboard, all in one project.

---

## 18.2 Caching and Lazy Loading

The fastest work is the work you don't do. Two powerful techniques are **caching** (reuse results instead of recomputing) and **lazy loading** (don't load things until needed).

### Caching

A **cache** stores the result of expensive work so the next request can reuse it. Caching happens at several layers:

| Layer | What is cached | Examples |
|---|---|---|
| **Browser cache** | Files and responses on the user's device | Images, scripts, CSS, controlled by HTTP headers |
| **CDN cache** | Copies of your content on servers near users | Static files, whole pages |
| **Application cache** | Results kept in memory or a fast store | Redis, Memcached, in-process memory |
| **Database cache / query results** | Frequently requested query results | A computed "top 10" list |

#### HTTP caching headers

A server tells browsers and CDNs how long a response may be reused through headers such as `Cache-Control`:

```text
Cache-Control: public, max-age=31536000, immutable
```

This means "any cache may store this, and it stays fresh for a year." It is perfect for build output whose filenames contain a content hash (17.1): if the file changes, its *name* changes, so users automatically get the new version. For data that changes often, use short lifetimes or `Cache-Control: no-cache`, which makes caches check with the server before reusing a copy. Validators such as `ETag` let the server answer `304 Not Modified` cheaply when nothing has changed.

#### A simple application cache

Here is the basic idea of a cache with a **time-to-live (TTL)**: if a stored result is still fresh, reuse it; otherwise compute it again.

```python
import time

_cache = {}


def cached(key, ttl_seconds, compute):
    entry = _cache.get(key)
    if entry and entry["expires"] > time.time():
        return entry["value"]          # cache hit: skip the slow work
    value = compute()                  # cache miss: do the work once
    _cache[key] = {"value": value, "expires": time.time() + ttl_seconds}
    return value


def slow_query():
    print("computing...")
    time.sleep(0.5)
    return 42


print(cached("answer", 60, slow_query))
print(cached("answer", 60, slow_query))
```

Output:

```text
computing...
42
42
```

The slow work ran once ("computing..." appears a single time), and the second call returned instantly from the cache. Real caching tools add size limits, eviction of old entries, and sharing between servers.

#### The hard part: invalidation

Caching creates a classic problem: **stale data**. If the underlying data changes but the cache still holds the old result, users see outdated information. Strategies include:

- **Short TTLs**: accept slight staleness for a limited time.
- **Explicit invalidation**: delete or update the cache entry when the data changes.
- **Versioned keys or filenames**: new data gets a new key, so old entries are simply ignored.

Cache only what is expensive to produce and acceptable to be slightly out of date. Never cache personalized or sensitive data in a shared cache by mistake.

### Lazy loading

**Lazy loading** means delaying the loading of resources until they are actually needed, so the first load is smaller and faster.

**1. Code splitting.** Instead of one giant JavaScript file, load parts of the app on demand. Bundlers split code at dynamic `import()` calls:

```javascript
// The settings page code downloads only when the user opens it
button.addEventListener("click", async () => {
  const { openSettings } = await import("./settings.js");
  openSettings();
});
```

Frameworks make this easy at the route level. React has `React.lazy`, Angular supports lazy-loaded routes, Vue.js supports async components, and Next.js splits code per page automatically.

**2. Lazy images.** Browsers can postpone loading offscreen images with one attribute:

```html
<img src="photo.jpg" alt="A mountain lake" loading="lazy" width="800" height="600">
```

Giving the `width` and `height` also lets the browser reserve space, preventing the page from jumping as images load.

**3. Pagination and infinite scroll.** Fetch data in pages instead of everything at once (Lesson 12).

**4. Lazy loading in mobile apps.** Flutter's `ListView.builder` and React Native's `FlatList` build only the list items currently on screen, which is essential for long lists.

---

## 18.3 Optimizing Bundle Size and Load Time

The less data you send, the faster the page loads, especially on slow mobile connections.

### Step 1: Measure

You cannot improve what you do not measure. Useful tools:

- **Browser DevTools**: the *Network* tab shows every download and its size and timing, and the *Performance* tab shows what the browser is busy doing.
- **Lighthouse** (built into Chrome's DevTools): scores performance and suggests specific improvements.
- **Bundle analyzers**: visualize which packages take up space in your JavaScript bundle.

### The Core Web Vitals

Google's **Core Web Vitals** are a widely used set of user-centered measurements:

| Metric | Measures | Plain meaning |
|---|---|---|
| **LCP** (Largest Contentful Paint) | Loading | How quickly the main content appears |
| **INP** (Interaction to Next Paint) | Responsiveness | How quickly the page reacts to clicks and taps |
| **CLS** (Cumulative Layout Shift) | Visual stability | Whether things jump around as the page loads |

### Step 2: Send less

| Technique | What it does |
|---|---|
| **Minification and tree-shaking** | Removes unneeded characters and unused code (usually automatic in production builds) |
| **Compression** | Servers compress text responses with **gzip** or **Brotli**, often shrinking them dramatically |
| **Code splitting** | Loads only the code needed for the current page (18.2) |
| **Fewer and lighter dependencies** | Importing a huge library to use one function bloats your bundle; check a package's size before adding it (Lesson 5) |
| **Import only what you use** | Import individual functions rather than an entire library where supported |

### Step 3: Optimize images and fonts

Images are usually the largest part of a page.

- Use modern formats such as **WebP** or **AVIF**, which are smaller than older ones at similar quality.
- **Resize** images to the size they are actually displayed, and offer several sizes with `srcset` for different screens.
- **Lazy-load** offscreen images (18.2).
- Limit web fonts to the weights you actually use, and give them sensible fallbacks.

Many frameworks include helpers: Next.js has an image component that resizes and serves modern formats automatically, and most meta-frameworks have similar tools.

### Step 4: Load wisely

- Use `defer` or `async` on scripts that don't need to block the page from appearing.
- Serve static assets from a **CDN** (Lesson 17).
- Use a modern protocol (HTTP/2 or HTTP/3), which most hosts enable by default, to load many files efficiently.
- Set a **performance budget**: an agreed limit (for example, a maximum bundle size) that your build or CI checks, so performance does not slowly erode.

### On the server side

Slow pages are not always a frontend problem. Slow database queries, repeated external calls, and too many queries per request (the N+1 problem from Lesson 14) are very common culprits. Logging how long each request and query takes (Lesson 13) points you to the slow ones.

---

## 18.4 Scaling a Framework-Based App

**Scaling** means handling more users, requests, or data without the application slowing down or failing. Not every app needs to scale massively, but understanding the options helps you design in a way that does not block you later.

### Vertical vs horizontal scaling

| | Vertical scaling ("scale up") | Horizontal scaling ("scale out") |
|---|---|---|
| **What** | Give one server more CPU and memory | Run several copies of the app |
| **Pros** | Simple; no code changes | No hard upper limit; survives a server failing |
| **Cons** | Has a ceiling; one point of failure | The app must be designed for it |

### Stateless servers

Horizontal scaling works smoothly only if each copy of your app is **stateless**: it keeps no user data in its own memory or on its own disk between requests. If server A holds your session and the next request goes to server B, you would be logged out. The fix is to keep shared state **outside** the app servers: in a database, a shared cache such as Redis, or in signed tokens (Lesson 15). Statelessness is one reason the HTTP design from Lesson 12 scales so well.

### Load balancers

A **load balancer** sits in front of several app servers and spreads incoming requests among them. It can also stop sending traffic to a server that is unhealthy.

```text
              ┌──────────► App server 1 ──┐
Users ─► Load balancer ───► App server 2 ──┼──► Shared database / cache
              └──────────► App server 3 ──┘
```

### The database is often the bottleneck

Adding app servers is relatively easy; the **database** is usually the first real limit. Common remedies:

- **Indexes** make lookups fast, as demonstrated below.
- **Fix inefficient queries** and avoid N+1 patterns.
- **Connection pooling** keeps the number of open connections under control.
- **Caching** keeps repeated reads away from the database (18.2).
- **Read replicas** are extra copies of the database used for reading, spreading the load.
- **Sharding** splits data across several databases (a major step; do it only when truly necessary).

An **index** is a data structure that lets the database find rows without scanning the entire table. Here, the same query is analyzed before and after adding an index on the `email` column of a table with 10,000 rows:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE users (id INTEGER PRIMARY KEY, email TEXT)")
conn.executemany(
    "INSERT INTO users (email) VALUES (?)",
    [(f"user{i}@example.com",) for i in range(10000)],
)

query = "EXPLAIN QUERY PLAN SELECT * FROM users WHERE email = ?"
params = ("user5000@example.com",)

print("Before:", conn.execute(query, params).fetchall()[0][-1])
conn.execute("CREATE INDEX idx_users_email ON users (email)")
print("After: ", conn.execute(query, params).fetchall()[0][-1])
```

Output:

```text
Before: SCAN users
After:  SEARCH users USING COVERING INDEX idx_users_email (email=?)
```

Before the index the database must **scan** every row; afterward it **searches** the index directly. On a table of millions of rows, that difference is the gap between a query taking seconds and one taking milliseconds. The trade-off is that indexes use storage and slow down writes slightly, so add them where queries actually need them.

### Move slow work out of the request

Some tasks, such as sending emails, generating reports, and processing uploaded videos, take too long to do while a user waits. Instead, put them on a **queue** and have separate **background workers** process them. The request returns immediately ("we're working on it"), and the work completes in the background. Most frameworks have a standard solution (for example, Laravel queues, Celery for Python, and BullMQ for Node.js).

### Other scaling tools

- **A CDN** for static assets and cacheable pages (Lesson 17).
- **Autoscaling**, where the platform adds and removes servers automatically as demand changes.
- **Monitoring and alerting** to see CPU, memory, error rates, and response times, so you know about problems before users report them.
- **Rate limiting** to protect the app from overload or abuse (Lesson 13).
- **Load testing** with tools that simulate many users, to find limits *before* real traffic does.

### Don't scale prematurely

Start with the simplest architecture that works, a well-organized monolith on a modest platform (Lesson 6), and let measurements guide you. Over-engineering for traffic you do not have adds cost and complexity without benefit, while a simple, stateless, cache-friendly, well-indexed application can serve a surprisingly large audience.

---

[Previous](./[17]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[19]-Choosing-A-Framework.md)
