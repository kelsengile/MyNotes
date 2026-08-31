[Previous](./[43]-Mobile-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[45]-Mobile-Security-Basics.md)

*Best Practices*

# Lesson 44 - Performance Optimization for Mobile

## 44.1 Why Mobile Performance Is Different

Unlike a desktop web app, mobile apps run on constrained hardware (limited CPU, memory, and battery) that varies wildly across devices, and users judge performance harshly — a laggy scroll or slow launch is one of the top reasons people abandon or uninstall an app.

---

## 44.2 Rendering Performance

Smooth UI requires the app to render each frame within its budget — typically **16ms per frame for 60fps**, or ~8ms for 120fps on newer high-refresh-rate displays. Dropped frames ("jank") usually come from doing too much work on the **main/UI thread** — heavy computation, large image decoding, or synchronous I/O should be moved to a background thread instead.

```kotlin
// Wrong: blocks the UI thread
val data = fetchLargeDataset()

// Right: runs off the main thread
viewModelScope.launch(Dispatchers.IO) {
    val data = fetchLargeDataset()
}
```

---

## 44.3 Memory Management

Excess memory use can cause the OS to terminate a backgrounded app or, in severe cases, crash it outright:

- **Image sizing** — decode images at the resolution they'll actually be displayed at, not their original (often much larger) file size.
- **List virtualization** — lists with many items (`LazyColumn` in Compose, `List` in SwiftUI, `FlatList` in React Native) only render the items currently visible on screen, rather than the entire dataset at once.
- **Retain cycles / leaks** — objects that reference each other in a way that prevents either from being freed; both platforms provide leak-detection tools (Instruments' Leaks tool, Android's LeakCanary library) to catch these.

---

## 44.4 App Startup Time

Users expect an app to become interactive quickly. Common culprits behind slow launches include heavy work done synchronously in the app's startup path (large dependency initialization, blocking network calls before the first screen renders) — deferring non-critical setup until after the first frame is shown is a standard fix.

---

## 44.5 Network and Battery Efficiency

- Batch network requests where possible rather than firing many small ones.
- Cache responses to avoid redundant fetches (see Lesson 17).
- Minimize use of continuous background location or sensor polling, since these are among the largest battery drains an app can cause (expanded on in Lesson 46).

Profiling tools (Lesson 36) are the most reliable way to find real bottlenecks — optimizing based on guesswork often targets the wrong part of the app entirely.

[Previous](./[43]-Mobile-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[45]-Mobile-Security-Basics.md)
