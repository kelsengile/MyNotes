[Previous](./[20]-Working-with-JSON.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[22]-Handling-Errors-and-Offline-States.md)

*Networking*

# Lesson 21 - Async Programming in Mobile Apps

## 21.1 The Main Thread

Every mobile app has a single **main (UI) thread** responsible for drawing the screen and responding to touches. If you run a slow operation — a network call, a large file read, heavy computation — directly on the main thread, the entire UI freezes until it finishes, producing the dreaded unresponsive, "frozen" app. Keeping the main thread free is one of the most important performance rules in mobile development.

---

## 21.2 async/await

Modern mobile languages use `async`/`await` to write asynchronous code that *reads* like normal sequential code, while the underlying operation actually runs without blocking the UI thread:

```dart
Future<void> loadProfile() async {
  setState(() => isLoading = true);
  final user = await fetchUser();     // suspends here, UI stays responsive
  setState(() {
    profile = user;
    isLoading = false;
  });
}
```

The function pauses at `await`, control returns to the UI (which keeps rendering and responding to input), and execution resumes automatically once the awaited operation completes.

---

## 21.3 Futures, Promises, and Coroutines

Each ecosystem has its own name for "a value that will exist later":

- **Dart**: `Future<T>`.
- **Swift**: `async` functions returning a value directly, or older completion-handler callbacks.
- **Kotlin**: **coroutines**, using `suspend` functions.
- **JavaScript (React Native)**: `Promise`.

All represent the same idea: a placeholder for a result that isn't ready yet, with a way to react once it is.

---

## 21.4 Running Work in Parallel

Sometimes you want multiple async operations running at once rather than one after another:

```dart
final results = await Future.wait([fetchUser(), fetchOrders(), fetchSettings()]);
```

This starts all three requests simultaneously and waits for all of them to finish, which is significantly faster than awaiting each one sequentially when they don't depend on each other.

## 21.5 Background Threads for Heavy Work

For CPU-intensive work that isn't I/O (like parsing a huge JSON file or processing an image), `async`/`await` alone isn't enough since it doesn't move the *computation itself* off the main thread. Frameworks provide dedicated mechanisms for this — Dart's `Isolate`, Kotlin's `Dispatchers.Default`, or Swift's `Task` with background priority — to run heavy computation on a separate thread entirely.

[Previous](./[20]-Working-with-JSON.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[22]-Handling-Errors-and-Offline-States.md)
