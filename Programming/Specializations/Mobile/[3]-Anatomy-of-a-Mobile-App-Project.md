[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[4]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 3 - Anatomy of a Mobile App Project

## 3.1 The Entry Point

Every mobile app has a single entry point where execution begins:

- **Flutter**: a `main()` function in `lib/main.dart` that calls `runApp()`.
- **React Native**: an `App.js`/`App.tsx` component registered with `AppRegistry.registerComponent`.
- **Native iOS**: an `@main` struct conforming to `App` (SwiftUI) or an `AppDelegate` (UIKit).
- **Native Android**: an `Application` subclass and a launcher `Activity` declared in the manifest.

Understanding the entry point matters because it's where global setup happens — things like initializing a database connection or configuring a crash reporter.

---

## 3.2 Folder Structure

While exact layouts vary by framework, most mobile projects share a similar shape:

- **Source code folder** (`lib/`, `src/`, `app/`) — where your screens, components, and logic live.
- **Assets folder** (`assets/`, `res/`) — images, fonts, and other static files bundled into the app.
- **Platform folders** (`ios/`, `android/`) — native project files that cross-platform frameworks generate and use to actually build the app for each OS.
- **Configuration files** — e.g., `pubspec.yaml` (Flutter), `package.json` (React Native), `Info.plist` (iOS), `AndroidManifest.xml` (Android). These declare app metadata like the app name, permissions, and dependencies.

---

## 3.3 The Manifest / Configuration Files

Two files are especially important to understand early:

- **`AndroidManifest.xml`**: declares every screen (`Activity`), background service, required permissions (like camera or location access), and the app's package name.
- **`Info.plist`** (iOS): declares the app's display name, version, supported orientations, and required permission usage descriptions (e.g., a string explaining *why* the app needs camera access, shown to the user in the permission prompt).

Both files are checked by the app stores during submission, and missing a required permission description is a common cause of app rejection.

---

## 3.4 Build Outputs

When you build a project, the toolchain produces an installable package:

- **Android**: an `.apk` (direct install file) or `.aab` (Android App Bundle, the format Google Play requires for distribution, which it then splits into optimized APKs per device).
- **iOS**: an `.ipa` file, which is signed with a developer certificate and provisioning profile before it can run on a real device or be submitted to the App Store.

[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[4]-Variables-and-Data-Types.md)
