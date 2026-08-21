[Previous](./[35]-Testing-Desktop-Applications.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[37]-Logging-and-Diagnostics.md)

*Architecture & Best Practices*

# Lesson 36 - Debugging & Profiling

## 36.1 Using a Debugger

A debugger lets you pause execution at a **breakpoint**, inspect variable values, step through code line by line, and evaluate expressions on the fly — far faster than debugging by inserting print statements. Every major IDE (Visual Studio, Xcode, VS Code, JetBrains) has an integrated debugger attachable to a running desktop app or launched alongside it.

---

## 36.2 Debugging Across Process Boundaries

Multi-process frameworks (Electron, Tauri) complicate debugging since the main process and renderer process(es) run separately — Electron apps typically debug the renderer through Chromium DevTools (`Ctrl+Shift+I`) and the main process through the Node debugger, often simultaneously. Native multi-threaded apps need the debugger to support switching between threads to inspect state on a background thread mid-operation.

---

## 36.3 Profiling Performance

A profiler measures where time and memory actually go while the app runs, rather than guessing. A **CPU profiler** samples the call stack repeatedly to show which functions consume the most time; a **memory profiler** tracks allocations and can reveal objects that are never released (leaks). Profile before optimizing — intuition about "the slow part" is frequently wrong, and profiling data prevents wasted effort optimizing code that wasn't actually the bottleneck.

---

## 36.4 Common Desktop Performance Culprits

Recurring causes worth profiling for specifically: layout thrashing (repeatedly recalculating UI layout in a tight loop), doing I/O or heavy computation on the UI thread (Lesson 22), unbounded caches or event-handler leaks that grow memory over a long session, and excessive re-rendering from overly broad data bindings (Lesson 17).

[Previous](./[35]-Testing-Desktop-Applications.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[37]-Logging-and-Diagnostics.md)
