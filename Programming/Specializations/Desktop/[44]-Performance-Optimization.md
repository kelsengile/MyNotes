[Previous](./[43]-Localization-and-Internationalization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[45]-Desktop-App-Security-Basics.md)

*Best Practices*

# Lesson 44 - Performance Optimization

## 44.1 Startup Time

Users judge an app's quality partly by how fast it launches. Common startup wins: defer loading anything not needed for the first visible screen (lazy-load plugins, secondary windows, rarely used data), avoid synchronous I/O or network calls during startup, and, for managed runtimes, consider ahead-of-time (AOT) compilation or trimming unused code to reduce startup and load time.

---

## 44.2 UI Responsiveness

Beyond keeping the UI thread free (Lesson 22), responsiveness also depends on rendering efficiently: avoid unnecessary re-layouts, virtualize long lists (only render the rows currently visible on screen rather than the entire dataset), and batch multiple small UI updates into one rather than triggering a re-render per change.

```csharp
// Virtualized lists only materialize visible rows, keeping a 100,000-row
// list as fast to scroll as a 20-row one.
```

---

## 44.3 Memory Usage

Watch for common desktop memory issues: event handlers registered but never unregistered (a classic leak — the subscriber keeps the object alive indefinitely), large cached data that's never evicted, and images/assets loaded at full resolution when a much smaller thumbnail would do. Long-running desktop apps (left open for days) are especially exposed to slow leaks that a short-lived process would never surface.

---

## 44.4 Measuring Before Optimizing

As with debugging (Lesson 36), profile before optimizing performance — the actual bottleneck is often not where intuition points. Set a measurable target (e.g. "cold start under 1.5s", "list scroll at 60fps") and profile against that specific target rather than optimizing generally, which tends to produce diminishing, hard-to-verify returns.

[Previous](./[43]-Localization-and-Internationalization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[45]-Desktop-App-Security-Basics.md)
