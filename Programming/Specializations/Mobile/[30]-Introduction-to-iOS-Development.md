[Previous](./[29]-Localization-and-Internationalization.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[31]-Introduction-to-Android-Development.md)

*Platform-Specific Development*

# Lesson 30 - Introduction to iOS Development (Swift, SwiftUI/UIKit)

## 30.1 The Swift Language

**Swift** is Apple's modern, statically-typed language for building iOS, iPadOS, macOS, watchOS, and tvOS apps. It replaced the older Objective-C as Apple's primary language because it's safer (optionals force you to handle missing values explicitly), faster, and more concise.

```swift
var username: String = "alex"
var age: Int? = nil // optional - may or may not hold a value

if let unwrappedAge = age {
    print("Age is \(unwrappedAge)")
} else {
    print("Age is unknown")
}
```

The `?` marks `age` as **optional**, meaning it can be `nil`. Swift forces you to unwrap optionals before using them, which prevents an entire class of crashes common in older languages.

---

## 30.2 SwiftUI vs UIKit

Apple offers two UI frameworks:

- **UIKit** — the original, imperative framework (since 2008). You build view hierarchies with `UIView` subclasses and update them manually via delegate/callback patterns.
- **SwiftUI** — a modern, declarative framework (since 2019). You describe *what* the UI should look like for a given state, and SwiftUI re-renders automatically when that state changes.

```swift
import SwiftUI

struct ContentView: View {
    @State private var count = 0

    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") { count += 1 }
        }
    }
}
```

SwiftUI is now the recommended starting point for new apps, though many production codebases still mix both frameworks since UIKit remains fully supported.

---

## 30.3 Xcode Project Structure

An iOS app project in Xcode is organized around a few key pieces:

- **`AppDelegate` / `SceneDelegate` (or `@main` App struct in SwiftUI)** — the entry point that configures the app's lifecycle and initial window.
- **`Info.plist`** — a configuration file declaring permissions, supported orientations, and app metadata.
- **Storyboards / `.xib` files** — visual layout files used by UIKit (SwiftUI apps typically skip these entirely).
- **Assets.xcassets** — the catalog holding app icons, images, and colors at multiple resolutions (`@1x`, `@2x`, `@3x`).

---

## 30.4 iOS-Specific Concepts

- **View Controllers** manage a screen's content and lifecycle in UIKit (`viewDidLoad`, `viewWillAppear`, etc.).
- **Auto Layout** is UIKit's constraint-based system for positioning views relative to each other and the screen, so layouts adapt across device sizes.
- **CocoaPods / Swift Package Manager (SPM)** are the two main ways to add third-party dependencies; SPM is Apple's native solution and is now generally preferred.

[Previous](./[29]-Localization-and-Internationalization.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[31]-Introduction-to-Android-Development.md)
