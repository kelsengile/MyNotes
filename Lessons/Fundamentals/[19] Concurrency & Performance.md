# Concurrency & Performance

## 19.1 Processes vs. Threads

### Processes

A **process** is an independent, running instance of a program, with its own dedicated memory space, file handles, and system resources, managed by the operating system.

- **Isolated memory** — one process cannot directly access another process's memory; the OS enforces this boundary.
- **Heavier to create** — spawning a new process involves the OS allocating a fresh memory space and resources, making it slower than creating a thread.
- **Crash isolation** — if one process crashes, it typically doesn't take down other processes (a key reliability benefit).
- **Inter-Process Communication (IPC)** — since processes don't share memory, they must communicate through explicit mechanisms: pipes, sockets, shared memory segments, message queues, or files.

```python
import multiprocessing

def worker(n):
    return n * n

if __name__ == "__main__":
    with multiprocessing.Pool(4) as pool:
        results = pool.map(worker, [1, 2, 3, 4])
    print(results)  # [1, 4, 9, 16] — each ran in a separate process
```

### Threads

A **thread** is a unit of execution *within* a process. A single process can have multiple threads, all sharing the same memory space and resources.

- **Shared memory** — threads within the same process can directly read/write the same variables and data structures, making communication between them fast and simple — but also introducing the risk of conflicts (see 19.3).
- **Lighter weight** — creating and switching between threads is cheaper than doing so for processes, since no new memory space needs to be allocated.
- **Shared failure domain** — an unhandled crash in one thread can potentially bring down the entire process (all its threads included).

```python
import threading

def worker(n, results, index):
    results[index] = n * n

results = [None] * 4
threads = [threading.Thread(target=worker, args=(i, results, i)) for i in range(4)]
for t in threads: t.start()
for t in threads: t.join()
print(results)
```

### Comparison

| | Process | Thread |
|---|---|---|
| Memory | Separate, isolated | Shared within the same process |
| Creation cost | Higher | Lower |
| Communication | IPC (pipes, sockets, queues) — slower | Shared memory — faster, but needs synchronization |
| Crash impact | Isolated to that process | Can affect the whole process |
| Best for | CPU-bound work needing true parallelism, fault isolation | I/O-bound work, tasks needing fast shared-state communication |

### The GIL (Global Interpreter Lock) — A Python-Specific Note

CPython (the standard Python implementation) has historically used a **Global Interpreter Lock**, allowing only one thread to execute Python bytecode at a time within a single process — meaning threads don't achieve true CPU parallelism for pure Python code (though they still help with I/O-bound work, since the GIL is released during I/O waits). This is why CPU-bound Python workloads often use `multiprocessing` (separate processes, each with its own interpreter and GIL) instead of `threading` to achieve real parallelism. Note: newer Python versions have introduced experimental "free-threaded" builds that remove this restriction.

### CPU-Bound vs. I/O-Bound Work

Choosing between processes, threads, or async depends heavily on what kind of work is being done:

- **CPU-bound** — the bottleneck is computation (image processing, numerical calculations). Benefits from true parallelism across multiple CPU cores → favors **multiple processes**.
- **I/O-bound** — the bottleneck is waiting on external operations (network requests, disk reads, database queries). The CPU is mostly idle, waiting → favors **threads or async code**, since many I/O operations can be in flight concurrently without needing multiple CPU cores.

## 19.2 Synchronous vs. Asynchronous Code

### Synchronous Execution

Operations run one after another, in order — each statement must complete before the next one begins. If a synchronous operation is slow (like a network call), the entire program blocks and waits.

```python
def get_user(user_id):
    response = requests.get(f"/users/{user_id}")   # blocks here until done
    return response.json()

user_a = get_user(1)   # waits
user_b = get_user(2)   # only starts after user_a finishes
```

### Asynchronous Execution

Operations can be initiated and the program continues doing other work while waiting for them to complete, instead of blocking. This is especially valuable for I/O-bound tasks, where the CPU would otherwise sit idle waiting on a network response or disk read.

```python
import asyncio

async def get_user(session, user_id):
    async with session.get(f"/users/{user_id}") as response:
        return await response.json()

async def main():
    async with aiohttp.ClientSession() as session:
        # Both requests run concurrently, not one after another
        user_a, user_b = await asyncio.gather(
            get_user(session, 1),
            get_user(session, 2)
        )

asyncio.run(main())
```

```javascript
// JavaScript — async/await
async function getUser(id) {
  const response = await fetch(`/users/${id}`);
  return response.json();
}

async function main() {
  const [userA, userB] = await Promise.all([getUser(1), getUser(2)]);
}
```

### Key Concepts

- **Blocking vs. non-blocking** — a blocking call halts the calling thread until it completes; a non-blocking call returns immediately, with the result delivered later (via a callback, promise, or awaited value).
- **Event loop** — the mechanism (used in JavaScript, Python's `asyncio`, etc.) that continuously checks for completed asynchronous operations and resumes the corresponding code, all on a single thread. This is how JavaScript achieves concurrency despite being single-threaded.
- **Callbacks** — the original pattern for async code: pass a function to be called once an operation completes. Prone to deeply nested code ("callback hell") when chaining many async steps.
- **Promises / Futures** — objects representing a value that will be available in the future, offering cleaner chaining (`.then()`) than raw callbacks.
- **async/await** — syntactic sugar over promises/futures that lets asynchronous code be written and read in a synchronous-looking style, while still being non-blocking underneath.

```javascript
// Callback style (older, harder to read as complexity grows)
getUser(1, (userA) => {
  getUser(2, (userB) => {
    console.log(userA, userB);
  });
});

// Promise style
getUser(1).then(userA => getUser(2).then(userB => console.log(userA, userB)));

// async/await (cleanest for sequential-looking logic)
const userA = await getUser(1);
const userB = await getUser(2);
```

### Concurrency vs. Parallelism (An Important Distinction)

- **Concurrency** — dealing with multiple tasks *making progress* over the same time period, potentially by interleaving them (not necessarily executing at the exact same instant). A single-threaded async event loop achieves concurrency without parallelism.
- **Parallelism** — multiple tasks executing *literally at the same time*, requiring multiple CPU cores (true parallelism generally requires multiple processes or threads on a multi-core system).

> Concurrency is about *structure* (juggling multiple tasks); parallelism is about *execution* (doing multiple things simultaneously). You can have one without the other — async I/O provides concurrency on a single thread, while `multiprocessing` provides genuine parallelism.

### When to Use Async

- **Good fit:** I/O-bound work with many concurrent operations — web servers handling many simultaneous requests, network calls, database queries, file I/O.
- **Poor fit:** CPU-bound work — async doesn't speed up raw computation, since the CPU is still the bottleneck, not waiting; use multiprocessing/parallelism for that instead.
- Async code has real costs: it requires "async-aware" libraries throughout the call chain, and mixing sync and async code carelessly can introduce subtle bugs (e.g., accidentally blocking the event loop with a synchronous call).

## 19.3 Race Conditions & Deadlocks

Concurrency introduces a category of bugs that don't exist in single-threaded code, arising from unpredictable timing and shared state.

### Race Conditions

A **race condition** occurs when the correctness of a program depends on the relative timing or interleaving of multiple threads/processes accessing shared data — and different orderings produce different (often incorrect) results.

```python
import threading

counter = 0

def increment():
    global counter
    for _ in range(100000):
        counter += 1   # NOT atomic: read, add, write — three separate steps

threads = [threading.Thread(target=increment) for _ in range(2)]
for t in threads: t.start()
for t in threads: t.join()

print(counter)  # Expected 200000, but often prints something LESS due to the race
```

**Why this happens:** `counter += 1` isn't a single atomic operation — it involves reading the current value, adding one, and writing it back. If two threads read the same value before either writes back, one increment gets lost.

### Preventing Race Conditions

- **Locks (mutexes)** — ensure only one thread can execute a critical section of code at a time.
```python
lock = threading.Lock()

def increment():
    global counter
    for _ in range(100000):
        with lock:            # only one thread can hold the lock at a time
            counter += 1
```
- **Atomic operations** — some languages/libraries provide operations guaranteed to complete as a single, uninterruptible step (e.g., atomic counters), avoiding the need for explicit locks in simple cases.
- **Immutable data** — data that can't be modified after creation eliminates the possibility of concurrent modification conflicts entirely; a common strategy in functional programming.
- **Thread-safe data structures** — many languages provide built-in concurrent collections (e.g., Java's `ConcurrentHashMap`, Python's `queue.Queue`) designed to handle simultaneous access safely.
- **Avoid shared mutable state where possible** — the message-passing model (threads/processes communicate via queues/channels rather than shared variables) sidesteps many race conditions by design (this is central to Go's concurrency philosophy: "share memory by communicating, don't communicate by sharing memory").

### Deadlocks

A **deadlock** occurs when two or more threads/processes are each waiting for a resource the other holds, so none of them can proceed — the program hangs indefinitely.

```python
lock_a = threading.Lock()
lock_b = threading.Lock()

def thread_1():
    with lock_a:
        time.sleep(0.1)
        with lock_b:      # waits for lock_b, held by thread_2
            pass

def thread_2():
    with lock_b:
        time.sleep(0.1)
        with lock_a:      # waits for lock_a, held by thread_1
            pass
# Both threads wait forever for a lock the other holds — deadlock
```

### The Four Necessary Conditions for Deadlock (Coffman Conditions)

A deadlock requires **all four** of these to hold simultaneously:
1. **Mutual exclusion** — a resource can only be held by one thread at a time.
2. **Hold and wait** — a thread holds one resource while waiting for another.
3. **No preemption** — a resource can't be forcibly taken away from the thread holding it.
4. **Circular wait** — a cycle of threads exists, each waiting on the next.

Breaking **any one** of these prevents deadlock.

### Preventing Deadlocks

- **Consistent lock ordering** — always acquire multiple locks in the same, globally agreed-upon order across the whole codebase, eliminating circular wait.
```python
# Both threads now acquire lock_a before lock_b — no circular wait possible
def thread_1():
    with lock_a:
        with lock_b:
            pass

def thread_2():
    with lock_a:   # same order as thread_1
        with lock_b:
            pass
```
- **Lock timeouts** — attempt to acquire a lock with a timeout, and back off/retry (or fail gracefully) if it can't be obtained, rather than waiting forever.
- **Minimize lock scope** — hold locks for the shortest time possible, and avoid holding one lock while waiting on another whenever feasible.
- **Use higher-level concurrency primitives** — queues, actor models, or transactional memory can sidestep manual lock management (and its pitfalls) entirely.

### Related Concurrency Hazards

- **Livelock** — similar to deadlock, but threads are actively changing state in response to each other without making real progress (like two people repeatedly stepping aside for each other in a hallway, in sync, forever).
- **Starvation** — a thread is perpetually denied the resources it needs to proceed, often because other threads are unfairly prioritized ahead of it.
- **Priority inversion** — a lower-priority thread holds a resource needed by a higher-priority thread, effectively "inverting" the intended priority order.

## 19.4 Basic Performance Optimization

### Measure Before Optimizing

> "Premature optimization is the root of all evil." — Donald Knuth

The first rule of performance work: **profile before you optimize**. Intuitions about where a program is slow are frequently wrong; a profiler shows where time and memory are *actually* being spent.

```python
import cProfile

cProfile.run("my_slow_function()")
# Shows exactly which functions consumed the most time and how often they were called
```

**Common profiling tools:** `cProfile`/`line_profiler` (Python), Chrome DevTools Performance tab (JavaScript), VisualVM/JProfiler (Java), `perf` (Linux systems-level profiling).

### The 80/20 Rule (Pareto Principle) in Performance

In most programs, a small portion of the code (often ~20%) accounts for the vast majority (~80%) of execution time. Optimization effort is best spent on these actual "hot paths" identified by profiling, rather than uniformly optimizing everything.

### Algorithmic Optimization (Usually the Biggest Win)

Before micro-optimizing code, check whether a better **algorithm or data structure** would help — this typically dwarfs any low-level tweak (see Section 16.1 on Big O).

```python
# O(n²) — checking for duplicates with nested loops
def has_duplicate_slow(items):
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j]:
                return True
    return False

# O(n) — using a set for O(1) average lookups
def has_duplicate_fast(items):
    seen = set()
    for item in items:
        if item in seen:
            return True
        seen.add(item)
    return False
```

### Common Optimization Techniques

- **Caching / memoization** — store the results of expensive computations and reuse them instead of recomputing (see Section 16.4 for a Fibonacci example). Applies at many levels: function-level memoization, HTTP caching, database query caching, CDN caching.
- **Reduce redundant work** — hoist invariant computations out of loops, avoid recalculating values that don't change between iterations.
```python
# Inefficient: len() and expensive_lookup() recomputed every iteration
for i in range(len(data)):
    if data[i] == expensive_lookup(constant_input):
        ...

# Better: compute once outside the loop
target = expensive_lookup(constant_input)
n = len(data)
for i in range(n):
    if data[i] == target:
        ...
```
- **Batching** — group multiple small operations into fewer, larger ones to reduce overhead (e.g., a single bulk database insert instead of thousands of individual inserts; batching network requests instead of firing one per item).
- **Lazy evaluation** — defer expensive work until it's actually needed, avoiding wasted computation on unused results (e.g., generators/iterators in Python instead of building a full list upfront).
- **Choosing the right data structure** — using a hash set for membership checks instead of a list (`O(1)` vs `O(n)`), or a deque instead of a list for frequent insertions/removals at both ends.
- **Reducing I/O overhead** — minimize the number of network round-trips or disk reads (e.g., fetching related data in one query with a `JOIN` rather than issuing an additional query per row — avoiding the classic "N+1 query problem").
- **Connection pooling** — reuse database/network connections rather than establishing a new one for every request, since connection setup has real overhead.
- **Parallelism** — for CPU-bound work, distributing computation across multiple cores/processes (see 19.1) can provide a near-linear speedup, up to the number of available cores.
- **Reducing memory allocations** — excessive object creation/garbage collection can be a hidden performance cost, especially in tight loops; reusing buffers/objects where sensible can help in performance-critical paths.

### Database-Specific Performance Tips

- **Add indexes** on columns used frequently in `WHERE`, `JOIN`, and `ORDER BY` clauses (see Section 14.3).
- **Avoid the N+1 query problem** — fetching a list, then querying again individually for each item's related data, instead of a single joined/batched query.
- **Use `EXPLAIN`/query plans** to understand how the database is actually executing a query and find missing indexes or inefficient scans.
- **Paginate large result sets** rather than loading everything into memory at once.

### Caveats and Trade-offs

- **Readability vs. speed** — micro-optimizations can make code harder to read and maintain; only trade clarity for speed where profiling shows it's genuinely worth it.
- **Optimize for the actual bottleneck** — speeding up code that isn't on the critical path (or isn't a measurable bottleneck) wastes effort and adds complexity for no real benefit.
- **Diminishing returns** — the biggest wins usually come early (fixing an `O(n²)` algorithm, adding a missing index); further micro-optimization typically yields progressively smaller gains for progressively more effort.
- **Re-measure after each change** — confirm that an optimization actually improved performance in practice; intuition can be misleading, and some "optimizations" make things worse under real-world conditions (e.g., due to caching effects or compiler optimizations already handling the naive case well).

### A Simple Optimization Workflow

1. **Set a concrete performance goal** (e.g., "API response under 200ms at p95") — optimization without a target has no clear stopping point.
2. **Profile** to find actual bottlenecks — don't guess.
3. **Optimize the biggest bottleneck first** — algorithmic fixes usually beat micro-optimizations.
4. **Re-measure** to confirm the change helped and quantify the improvement.
5. **Repeat** until the goal is met, then stop — further optimization has diminishing, and eventually negative, returns on effort.