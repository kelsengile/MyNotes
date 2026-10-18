[Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[2]-Dart-Essentials.md)

*Basics*

# Lesson 1 - What Is Flutter

Flutter is a UI toolkit from Google. You write one codebase in Dart and compile it to Android, iOS, web, Windows, macOS, and Linux. This lesson explains what that means, how Flutter draws things, and how to get a project running.

---

## 1.1 Cross-platform development explained

There are three common ways to build apps for several platforms:

| Approach | How it works | Example |
|---|---|---|
| **Native** | A separate app per platform, using each platform's own language and UI kit | Kotlin + Jetpack Compose, Swift + SwiftUI |
| **Web wrapper** | A web app inside a native shell | Ionic, Cordova |
| **Cross-platform toolkit** | One codebase compiled to native code | Flutter, React Native |

Flutter's goal is **write once, run anywhere** without the app feeling like a website. Flutter apps compile ahead-of-time (AOT) to native ARM/x64 machine code for release builds. During development they run in a virtual machine, which is what makes hot reload possible.

**Trade-offs to know about:**

- You get one codebase, a consistent look on every platform, and fast iteration.
- App size is slightly larger than a minimal native app, because the engine ships with it.
- Brand-new platform features sometimes need a plugin or a bit of native code before Flutter can use them.

---

## 1.2 How Flutter renders UI

Most toolkits ask the operating system to draw a native button. Flutter does not. It has its own **rendering engine** and draws every pixel itself onto a canvas, much like a game engine.

Flutter has three layers:

1. **Framework (Dart)**: widgets, layout, animation, gestures. This is the layer you work in.
2. **Engine (C++)**: the renderer (Impeller on modern versions, Skia on older ones), text layout, and the Dart runtime.
3. **Embedder (per platform)**: connects the engine to the OS for windows, input, and the event loop.

Inside the framework, three parallel trees are involved:

- **Widget tree**: lightweight, immutable descriptions of the UI. You write these.
- **Element tree**: the living instances that connect widgets to the render tree and track where each widget sits.
- **RenderObject tree**: the objects that actually perform layout and painting.

Widgets are cheap to recreate. When state changes, Flutter rebuilds widgets and compares them with the existing elements. It only updates the render objects that really changed.

---

## 1.3 Installing the SDK and flutter doctor

1. Download the SDK from the official install page (see the Introduction file) and extract it somewhere permanent.
2. Add the `flutter/bin` folder to your `PATH`.
3. Install an editor: **VS Code** (with the Flutter extension) or **Android Studio** (with the Flutter plugin).
4. Install the target platform toolchains, such as the Android SDK, or Xcode on macOS for iOS.
5. Run the health check:

```bash
flutter doctor
```

`flutter doctor` inspects your machine and prints a checklist:

```
[✓] Flutter (Channel stable)
[✓] Android toolchain
[✗] Xcode - not installed
[✓] Chrome - develop for the web
[✓] Connected device
```

Each `✗` or `!` comes with a hint on how to fix it. Run `flutter doctor -v` for detail.

Create and run your first app:

```bash
flutter create my_app
cd my_app
flutter run
```

If several devices are connected, `flutter devices` lists them and `flutter run -d <device-id>` picks one.

---

## 1.4 Tour of a Flutter project

A freshly created project looks like this:

```
my_app/
├── lib/
│   └── main.dart          # app entry point
├── test/
│   └── widget_test.dart   # sample test
├── android/               # Android host project
├── ios/                   # iOS host project
├── web/                   # web host files
├── windows/ macos/ linux/ # desktop host projects
├── pubspec.yaml           # dependencies, assets, fonts
└── analysis_options.yaml  # lint rules
```

**Where you spend your time:** `lib/` holds your Dart code, and `pubspec.yaml` holds your dependencies and assets.

The smallest useful `main.dart`:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: Scaffold(
        body: Center(child: Text('Hello, Flutter!')),
      ),
    );
  }
}
```

- `main()` is where every Dart program starts.
- `runApp()` inflates the given widget and attaches it to the screen.
- `MaterialApp` sets up theming, navigation, and localization.
- `Scaffold` provides the basic page structure.

While `flutter run` is active, press **`r`** for hot reload (keeps state), **`R`** for hot restart (resets state), and **`q`** to quit.

---

[Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[2]-Dart-Essentials.md)