[Previous](./[35]-Testing-Mobile-Apps.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[37]-App-Icons-and-Splash-Screens.md)

*Architecture & Best Practices*

# Lesson 36 - Debugging & Profiling

## 36.1 Breakpoints and Step Debugging

Both Xcode and Android Studio let you set **breakpoints** — lines where execution pauses so you can inspect variables, step through code line-by-line (`step over`, `step into`, `step out`), and evaluate expressions live in a debug console. This is the primary tool for understanding *why* code isn't behaving as expected, rather than guessing from `print` statements alone.

---

## 36.2 Logging

Structured logging is often faster than breakpoints for tracking down intermittent issues, since it doesn't pause execution:

```swift
print("User logged in: \(user.id)")
```

```kotlin
Log.d("AuthActivity", "User logged in: ${user.id}")
```

Both platforms let you filter logs by tag/severity level in their respective consoles (Xcode's console, Android Studio's **Logcat**), which is essential once an app is producing hundreds of log lines.

---

## 36.3 Profiling Performance

Profilers measure your app's real resource usage while it runs, rather than guessing:

- **Xcode Instruments** — profiles CPU usage, memory allocations/leaks, energy usage, and network activity on iOS.
- **Android Studio Profiler** — the equivalent toolset for CPU, memory, network, and energy on Android.

Common things to look for: memory that keeps climbing and never drops (a **leak**), spikes in CPU usage during scrolling (dropped frames), and unexpectedly large or frequent network calls.

---

## 36.4 Crash Analysis

When an app crashes, it produces a **stack trace** — the sequence of function calls active at the moment of the crash — pointing to the exact line that failed. Reading a stack trace from the top down usually reveals the root cause, though the true bug is sometimes several calls "up" the stack from where the crash actually occurred.

- **Symbolication** — the process of converting a crash's raw memory addresses back into readable function/line names, required for crashes from release (optimized) builds.
- Tools like **Crashlytics** (covered further in the Analytics lesson) automatically collect, symbolicate, and group crashes from real users in production.

---

## 36.5 Debugging on Real Devices

Simulators/emulators are convenient but don't perfectly replicate real-world conditions — actual device hardware, low-memory situations, poor network connectivity, and background app interruptions can all surface bugs that never appear in a simulator. Testing on physical devices periodically throughout development, not just before release, catches these issues earlier.

[Previous](./[35]-Testing-Mobile-Apps.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[37]-App-Icons-and-Splash-Screens.md)
