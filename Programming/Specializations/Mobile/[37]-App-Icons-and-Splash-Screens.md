[Previous](./%5B36%5D-Debugging-and-Profiling.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[38]-Preparing-for-Release.md)

*Publishing & Distribution*

# Lesson 37 - App Icons, Splash Screens & Assets

## 37.1 App Icons

The app icon is the image users see on their home screen and in the App Store/Google Play listing. Both platforms require the icon at **multiple resolutions**, since it's displayed at different sizes across home screens, settings, notifications, and store listings:

- **iOS** — a single `1024x1024` master image is now standard; Xcode automatically generates the smaller sizes needed for older OS versions and contexts.
- **Android** — requires several explicit sizes (`mdpi`, `hdpi`, `xhdpi`, `xxhdpi`, `xxxhdpi`) plus an **adaptive icon** (separate foreground and background layers) so the system can mask the icon into different shapes (circle, squircle, etc.) depending on the device manufacturer's launcher.

---

## 37.2 Splash Screens / Launch Screens

A splash screen (iOS calls it a "Launch Screen") displays briefly while the app's initial code loads, avoiding a jarring blank white screen on startup:

- **iOS** uses a `LaunchScreen` storyboard or, on modern Xcode, a lightweight `Launch Screen` asset — intentionally kept minimal since it can't run any real code.
- **Android** uses a themed splash screen defined via a style/theme resource (or the modern `SplashScreen` API introduced in Android 12), showing a background color and centered icon.

The best practice on both platforms is to keep splash screens simple and static — they should feel like a seamless continuation of the app icon, not a full loading animation.

---

## 37.3 Managing Image Assets

Since devices have varying pixel densities, images need multiple resolution variants to look sharp without wasting memory:

- **iOS** — `@1x`, `@2x`, `@3x` suffixes (e.g. `icon.png`, `icon@2x.png`, `icon@3x.png`), or a single vector `.pdf`/SVG-like asset that scales automatically.
- **Android** — density buckets (`drawable-mdpi`, `drawable-hdpi`, `drawable-xhdpi`, etc.), or **vector drawables** (`.xml`) that scale to any resolution without needing multiple files.

Vector formats (SDF on iOS, Vector Drawables on Android) are generally preferred for icons and simple graphics since one file covers every screen density.

---

## 37.4 Asset Catalogs and Organization

Both platforms provide a centralized system for managing assets — Xcode's **Asset Catalog** (`.xcassets`) and Android's **`res/`** resource directories — which also handle **dark mode variants**, letting you supply a different image or color depending on whether the system is in light or dark appearance.

[Previous](./%5B36%5D-Debugging-and-Profiling%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[38]-Preparing-for-Release.md)
