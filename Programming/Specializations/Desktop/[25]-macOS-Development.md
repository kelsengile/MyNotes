[Previous](./[24]-Windows-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[26]-Linux-Desktop-Development.md)

*Platform-Specific & Cross-Platform Frameworks*

# Lesson 25 - macOS Development (AppKit/SwiftUI)

## 25.1 AppKit vs SwiftUI

macOS native development uses Swift (or Objective-C) with one of two UI frameworks:

- **AppKit** — the mature, imperative UI framework, giving fine-grained control and covering nearly every macOS UI capability.
- **SwiftUI** — Apple's modern declarative framework, describing UI as a function of state, shared conceptually (though not code-compatible) with iOS development.

Most new macOS apps start in SwiftUI and drop into AppKit via interop for capabilities SwiftUI doesn't yet cover.

---

## 25.2 SwiftUI Basics

```swift
struct ContentView: View {
    @State private var name: String = ""

    var body: some View {
        VStack {
            Text("Enter your name:")
            TextField("Name", text: $name)
            Button("Submit") {
                print("Hello, \(name)!")
            }
        }
        .padding()
    }
}
```

`@State` marks a property whose changes automatically re-render the view — SwiftUI's version of the data binding concept from Lesson 17.

---

## 25.3 macOS Conventions

macOS apps follow distinct conventions: a shared menu bar at the top of the screen (not per-window), closing the last window doesn't quit the app by default, and windows commonly support full "traffic light" (close/minimize/zoom) controls with native window-tab support. Following the Human Interface Guidelines (HIG) closely matters more on macOS than on other platforms, since users notice deviations from platform norms.

---

## 25.4 Sandboxing and Distribution

Apps distributed through the Mac App Store must run inside the **App Sandbox**, restricting filesystem/network access to explicitly declared entitlements. Apps distributed outside the App Store must still be **notarized** by Apple (Lesson 39) or macOS's Gatekeeper will block them from launching.

[Previous](./[24]-Windows-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[26]-Linux-Desktop-Development.md)
