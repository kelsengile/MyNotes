[Previous](./[21]-Working-with-JSON-and-Serialization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[23]-Async-Programming-for-Responsive-UIs.md)

*Concurrency*

# Lesson 22 - Background Tasks & Threading

## 22.1 The UI Thread

Nearly every desktop framework requires all UI updates to happen on a single **UI thread** (also called the main thread). This thread also runs the event loop from Lesson 15, so anything slow running on it — a large file parse, a heavy computation, a synchronous network call — freezes the entire window until it finishes.

---

## 22.2 Spawning Background Threads

Long-running work should run on a separate thread, leaving the UI thread free to keep responding to input:

```csharp
Task.Run(() =>
{
    var result = DoExpensiveWork();
    // must marshal back to the UI thread to touch UI controls:
    Dispatcher.Invoke(() => resultLabel.Text = result);
});
```

Touching UI controls directly from a background thread is a common source of crashes or corrupted rendering — nearly every framework requires marshaling the result back to the UI thread via a dispatcher/invoke mechanism.

---

## 22.3 Thread Safety

When multiple threads read and write shared data, race conditions can corrupt it. Protect shared state with synchronization primitives (locks/mutexes) or, better, avoid sharing mutable state across threads at all — pass immutable data or use thread-safe collections (`ConcurrentDictionary`, channels/queues) instead.

```csharp
lock (_syncRoot)
{
    _sharedCounter++;
}
```

---

## 22.4 Thread Pools

Creating a new OS thread for every small task is wasteful. A **thread pool** maintains a reusable set of worker threads and queues tasks onto them, amortizing thread-creation cost. Most frameworks expose a high-level task API (`Task.Run`, Node's worker threads, Rust's `tokio`/`rayon`) backed by a thread pool, so you rarely manage raw threads directly.

[Previous](./[21]-Working-with-JSON-and-Serialization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[23]-Async-Programming-for-Responsive-UIs.md)
