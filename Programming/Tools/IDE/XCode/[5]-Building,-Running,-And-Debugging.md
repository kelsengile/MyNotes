[Previous](./[4]-Interface-Builder-And-SwiftUI.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[6]-Testing-In-Xcode.md)

*Building And Debugging Apps*

# Lesson 5 - Building, Running, And Debugging

## 5.1 Simulators vs Physical Devices

The destination selector in the toolbar (Lesson 2.3) lets you run your app in two fundamentally different environments:

| | Simulator | Physical Device |
|---|---|---|
| Speed to launch | Fast, no cabling needed | Requires USB/Wi-Fi connection and trust pairing |
| Accuracy | Runs on your Mac's CPU/GPU, not real device hardware | True performance and hardware behavior |
| Hardware features | Camera, GPS, and sensors are simulated/limited | Full access to real camera, GPS, accelerometer, etc. |
| Signing requirement | None | Requires a signing certificate and provisioning profile (Lesson 7.1) |

```
Scheme: MyApp
Destination:  [ iPhone 15 Pro (Simulator) ▾ ]
              [ Jane's iPhone (Physical)    ]
```

Simulators are ideal for rapid iteration on layout and logic, but certain things can only be verified on a real device: actual camera/GPS behavior, true performance and battery impact, Face ID/Touch ID prompts, and push notification delivery.

---

## 5.2 Build Configurations (Debug/Release) And Schemes

A **Scheme** bundles together what to build, how to build it, and what action (run, test, archive) to perform — configurable via **Product → Scheme → Edit Scheme**.

```
Scheme: MyApp
 ├── Run       → uses "Debug" configuration
 ├── Test      → uses "Debug" configuration
 ├── Profile   → uses "Release" configuration
 └── Archive   → uses "Release" configuration
```

- **Debug configuration** — includes debug symbols, disables optimizations, and enables assertions, making it slower but far easier to step through and inspect.
- **Release configuration** — applies compiler optimizations and strips debug info, producing the smaller, faster build meant for the App Store or performance testing.
- **Multiple schemes per project** — larger projects often define separate schemes for different app targets or environments (e.g. "MyApp-Staging" pointed at a test API, "MyApp-Production" pointed at the live API), switchable from the same scheme dropdown covered in Lesson 2.3.

---

## 5.3 Breakpoints And The Debug Navigator

Breakpoints work much like in any other IDE: click the gutter next to a line number to set one (a blue arrow appears), then run (`Cmd+R`) to trigger it.

```
12   func calculateTotal(items: [Item]) -> Double {
13 ➤     let total = items.reduce(0) { $0 + $1.price }  ← breakpoint
14       return total
15   }
```

Once paused, the **Debug Navigator** (Navigator pane, the speedometer-like icon) shows two linked views:

- **Variables View** — local variables and `self`'s properties in the current scope, expandable for nested objects.
- **CPU/Memory graphs** — a live, at-a-glance view of the app's resource usage while it runs, without needing to open the separate Instruments tool (Lesson 6.3) for a quick check.

Stepping controls appear in the debug bar at the bottom of the window: Continue, Step Over, Step Into, and Step Out — functionally identical to the equivalents covered in the JetBrains and VS Code Topics, just presented in Xcode's own toolbar style.

**Symbolic breakpoints** — set via the Breakpoint Navigator, these pause execution whenever a named function is called anywhere in the app (even system frameworks), rather than at a specific line you've written — useful for catching exactly when/where a specific method fires.

---

## 5.4 The Console And LLDB

The **Console** (bottom of the window, alongside the Variables View) shows your app's printed output (`print()` statements) and serves as a front-end to **LLDB**, the debugger Xcode uses under the hood.

```
(lldb) po items
▿ 3 elements
  - price: 9.99
  - price: 14.50
  - price: 3.25

(lldb) po items.count
3
```

- **`po` (print object)** — evaluates and prints a Swift/Objective-C expression, similar to "Evaluate Expression" in JetBrains IDEs or the Debug Console REPL in VS Code.
- **`p`** — prints a simpler value without invoking the full object description machinery `po` uses; slightly faster for primitive types.
- **Custom LLDB commands** — the console accepts real LLDB commands (e.g. `bt` for a full backtrace, `expr` to run and even modify a variable's value live), giving far more control than the point-and-click debugger alone.

Because LLDB is a full command-line debugger, experienced iOS developers often mix GUI stepping (breakpoints, the Variables View) with typed console commands for anything more specific than what the point-and-click tools expose directly.

[Previous](./[4]-Interface-Builder-And-SwiftUI.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[6]-Testing-In-Xcode.md)
