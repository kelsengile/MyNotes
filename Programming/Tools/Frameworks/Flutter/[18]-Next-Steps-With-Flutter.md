[Previous](./[17]-Building-And-Publishing.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md)

*Quality And Delivery*

# Lesson 18 - Next Steps With Flutter

You now know the full path from first screen to published app. This final lesson points to where to grow: structuring larger apps, targeting more platforms, mixing with native code, and building portfolio projects.

---

## 18.1 Clean architecture

As an app grows, organization matters more than any single widget. The goal of **architecture** is to separate concerns so code stays testable and changeable.

**A common layered structure:**

```
lib/
├── core/               # shared utilities, constants, theme, errors
├── data/               # talks to the outside world
│   ├── models/         # JSON models (DTOs)
│   ├── sources/        # API clients, local databases
│   └── repositories/   # implementations of domain repositories
├── domain/             # pure Dart business rules
│   ├── entities/       # core objects
│   ├── repositories/   # abstract interfaces
│   └── usecases/       # single actions, e.g. LoginUser
└── presentation/       # UI
    ├── screens/
    ├── widgets/
    └── state/          # notifiers, cubits, blocs
```

**The dependency rule:** dependencies point **inward**. UI depends on domain, and data depends on domain, but the domain knows nothing about Flutter, HTTP, or databases. This lets you swap an API for a local database without touching business logic.

**An alternative: feature-first** structure groups everything by feature, which scales well with teams:

```
lib/
└── features/
    ├── auth/        (data, domain, presentation)
    ├── cart/
    └── profile/
```

Flutter's own architecture guidance recommends separating a **UI layer** (views + view models), an optional **domain layer**, and a **data layer** (repositories + services).

**Supporting practices:**

- **Dependency injection** (constructor injection, Riverpod providers, or `get_it`) so classes receive their dependencies.
- **Repository pattern:** one place that decides whether data comes from the network or cache.
- **Immutable models** (`freezed` or hand-written) and clear error types.
- **Lints:** enable `flutter_lints` or stricter rules in `analysis_options.yaml`.

Do not over-engineer. A small app needs only simple folders, and you can add layers when the pain appears.

---

## 18.2 Flutter for web and desktop

The same codebase can target more platforms.

**Enable and run:**

```bash
flutter config --enable-web        # usually already enabled
flutter create .                   # add missing platform folders to a project
flutter run -d chrome
flutter run -d windows             # or macos, linux
```

**Web considerations:**

- Flutter web renders to a canvas (using CanvasKit or WebAssembly builds), which suits **app-like experiences** (dashboards, tools, PWAs) better than content sites that need great SEO.
- Initial load size matters. Use deferred loading (`deferred as`) to split code.
- Set up URL routing (Lesson 10) so links and the browser back button work.
- Avoid `dart:io`. Use packages with web support.
- Host the `build/web` folder on Firebase Hosting, Netlify, GitHub Pages, or any static host.

**Desktop considerations:**

- Support **mouse, keyboard shortcuts, resizable windows**, and menus (Lesson 8).
- Use `window_manager` to control window size and title.
- Package installers: MSIX for Windows, DMG/PKG for macOS, Snap/AppImage/deb for Linux.
- Desktop plugins vary, so check each package's supported platforms on pub.dev.

**Also possible:** embedded devices, and Flutter running inside existing apps (see next section).

**Mindset:** write platform-neutral business logic once, and isolate platform differences behind small interfaces so you can swap implementations per platform.

---

## 18.3 Native integration

Flutter can coexist with native code in two directions.

**1. Calling native from Flutter** (covered in Lesson 14):

- **Platform channels** (`MethodChannel`, `EventChannel`) for custom features.
- **Pigeon** to generate type-safe channel code.
- **FFI** (`dart:ffi`) to call C/C++ libraries directly, and `ffigen` to generate bindings. Newer tooling such as **`jnigen`** (Java/Kotlin) and **`ffigen` for Objective-C/Swift** bridges native APIs without hand-written channels.
- **Platform views** (`AndroidView`, `UiKitView`, `HtmlElementView`) embed native UI, such as a native map view, inside a Flutter widget tree.

**2. Embedding Flutter in an existing native app ("add-to-app"):**

- Add a Flutter **module** to an existing Android or iOS project (`flutter create --template=module my_module`).
- Show Flutter content through a `FlutterActivity`/`FlutterFragment` on Android or a `FlutterViewController` on iOS.
- Use a **cached FlutterEngine** to make the Flutter screen open quickly.
- Good for migrating gradually or adding one cross-platform feature to a large native app.

**Publishing your own plugin:** `flutter create --template=plugin --platforms=android,ios my_plugin`, then document it, add tests, and publish with `dart pub publish`.

**When to go native:** extremely platform-specific features (certain AR, widgets/complications, system extensions) often need native code. Flutter can host the rest of the app.

---

## 18.4 Portfolio app ideas

Building real projects is the fastest way to solidify skills and impress employers. Choose projects that exercise different lessons:

| Project | Skills practiced |
|---|---|
| **To-do / notes app** | Forms, lists, state management, local storage (Drift or Isar) |
| **Weather app** | REST API, JSON parsing, `FutureBuilder`/Riverpod, location permission |
| **Expense tracker** | Charts, SQLite, filtering, theming, dark mode |
| **Recipe browser** | Search, grids, Hero animations, favorites, caching |
| **Chat app** | Firebase Auth, Firestore streams, push notifications |
| **Habit tracker** | Local notifications, calendar UI, animations |
| **E-commerce mock** | Navigation (go_router), cart state, checkout form, responsive layout |
| **Fitness / workout timer** | Timers, animations, background behavior, sensors |
| **Photo journal** | Camera, gallery, storage, maps/geolocation |
| **Flashcard/quiz app** | Spaced repetition logic, unit tests, state, persistence |

**Make projects portfolio-worthy:**

1. **Clean code and architecture** (18.1), with a clear folder structure.
2. **Tests** for core logic and key widgets.
3. **Polished UI:** consistent theme, dark mode, responsive layout, loading and error states, empty states.
4. **A good README** with screenshots or a short GIF, features, setup steps, and the tech used.
5. **CI** running analysis and tests on every push.
6. **Publish it:** at least a web demo or a Play Store internal testing link.
7. **Write down decisions:** why you chose a state solution or database.

**Keep learning:**

- Official docs at docs.flutter.dev, plus the Flutter YouTube channel's "Widget of the Week" and "Decoding Flutter" series.
- Read source code of well-made open-source Flutter apps.
- Explore **Dart 3 features** (records, patterns, sealed classes) and the **Flutter Casual Games Toolkit** with Flame.
- Contribute to a package or to Flutter itself.

Pick one project, ship it, then pick the next. Congratulations on completing the Flutter Topic!

---

[Previous](./[17]-Building-And-Publishing.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md)