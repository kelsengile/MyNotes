[Previous](./[2]-The-Xcode-Interface.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[4]-Interface-Builder-And-SwiftUI.md)

*Interface And Project Structure*

# Lesson 3 - Swift And Project Structure Basics

## 3.1 Swift As Xcode's Primary Language

Swift is Apple's modern, type-safe language, and the default choice for new projects since around 2014, gradually replacing the older Objective-C in most new codebases. Xcode fully supports both, and many long-running apps mix the two within a single project via a generated "bridging header."

```swift
// A simple SwiftUI-flavored Swift snippet
struct Greeting {
    let name: String

    func message() -> String {
        "Hello, \(name)!"
    }
}

let greeting = Greeting(name: "World")
print(greeting.message())   // Hello, World!
```

Notable Swift language traits relevant to working in Xcode:

- **Optionals (`String?`)** — Swift's built-in way of representing "a value or nothing," forcing you to explicitly handle the absence of a value rather than risking a crash from an unexpected `nil`.
- **Type inference** — `let name = "World"` infers `String` without an explicit annotation, though Xcode's autocomplete still shows the inferred type when you hover.
- **Playgrounds** — a special Xcode document type (`.playground`) for running Swift code interactively with live, inline results, useful for testing an idea before adding it to a full project.

---

## 3.2 The App Entry Point (App Delegate/Scene Delegate vs SwiftUI's App Struct)

How an app "starts" differs depending on whether the project uses UIKit or SwiftUI as its primary interface layer:

**UIKit (traditional):**

```swift
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication,
        didFinishLaunchingWithOptions ...) -> Bool {
        // App-wide setup (e.g. configure analytics, push notifications)
        return true
    }
}
```

- **AppDelegate** — handles app-wide lifecycle events (launch, background, termination).
- **SceneDelegate** — handles per-window/scene lifecycle, introduced when iOS added multi-window support (e.g. Split View on iPad).

**SwiftUI (modern):**

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

- The `@main` attribute marks the entry point in both cases.
- SwiftUI's `App` protocol collapses AppDelegate/SceneDelegate concepts into a single declarative struct, though `UIApplicationDelegateAdaptor` can still bridge to a traditional AppDelegate when needed (e.g. for certain push-notification APIs).

---

## 3.3 Info.plist And Project Settings

**Info.plist** is a property list file storing an app's metadata and configuration — read by iOS at launch to determine permissions, display name, and supported capabilities.

```xml
<key>CFBundleDisplayName</key>
<string>My App</string>
<key>NSCameraUsageDescription</key>
<string>This app uses the camera to scan documents.</string>
```

Common entries you'll edit directly or through Xcode's "Info" tab in project settings:

| Key | Purpose |
|---|---|
| `CFBundleDisplayName` | The name shown under the app icon |
| `NSCameraUsageDescription` | Required permission-prompt text before accessing the camera |
| `UISupportedInterfaceOrientations` | Which device orientations the app supports |
| `CFBundleShortVersionString` | The user-facing version number (e.g. "1.2.0") |

Beyond Info.plist, the **project settings editor** (click the blue project icon at the top of the Project Navigator) has tabs for **General** (bundle ID, deployment target, app icon), **Signing & Capabilities** (covered in [Lesson 7.1](./[7]-Archiving-And-Distributing-An-App.md)), and **Build Settings** (compiler flags, optimization levels).

---

## 3.4 Frameworks And Dependencies (Swift Package Manager)

Modern Xcode projects manage third-party dependencies primarily through **Swift Package Manager (SPM)**, built directly into Xcode — no separate tool install required, unlike CocoaPods (an older, still-used community-built alternative that requires a Ruby-based command-line tool).

```
File → Add Package Dependencies...
 ┌─────────────────────────────────────────┐
 │ Search or enter package URL:              │
 │ https://github.com/Alamofire/Alamofire    │
 │                                            │
 │ Dependency Rule: Up to Next Major Version  │
 └─────────────────────────────────────────┘
```

- **Package.swift** — for projects that are themselves packages (or for local package targets within a larger app), this manifest file declares the package's own dependencies and products.
- **Version resolution** — Xcode locks resolved versions in a `Package.resolved` file, which should be committed to version control so every teammate builds against identical dependency versions.
- **CocoaPods and Carthage** — older dependency managers still found in many existing projects; CocoaPods generates an `.xcworkspace` file that must be opened instead of the plain `.xcodeproj` once it's introduced.

```
MyApp.xcodeproj      ← no external pod dependencies
MyApp.xcworkspace     ← open this instead once CocoaPods is added
```

[Previous](./[2]-The-Xcode-Interface.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[4]-Interface-Builder-And-SwiftUI.md)
