[Previous](./[1]-What-is-Mobile-Development.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[3]-Anatomy-of-a-Mobile-App-Project.md)

*Getting Started*

# Lesson 2 - Development Environment & Toolchains (Xcode, Android Studio, Flutter/RN CLI)

## 2.1 Xcode (iOS)

Xcode is Apple's official IDE and is required for building, testing, and submitting iOS apps — it only runs on macOS. It bundles the Swift compiler, Interface Builder, a suite of iOS simulators (virtual iPhones/iPads you can run without owning the hardware), and Instruments, a profiling tool for measuring performance and memory. Even developers using cross-platform frameworks usually still need Xcode installed on a Mac, because it provides the underlying build tools for iOS.

---

## 2.2 Android Studio

Android Studio is Google's official IDE for Android development, built on JetBrains' IntelliJ platform, and it runs on Windows, macOS, and Linux. It includes the Android SDK (Software Development Kit), an emulator for running virtual Android devices, a visual layout editor, and Gradle, the build system that compiles code and packages it into an installable app file (an APK or AAB).

---

## 2.3 Cross-Platform CLIs

Flutter and React Native are primarily driven from the command line rather than a single dedicated IDE:

- **Flutter CLI**: the `flutter` command creates projects, runs apps, and manages packages (`flutter create`, `flutter run`, `flutter pub get`). Flutter apps are commonly edited in VS Code or Android Studio using a Flutter plugin.
- **React Native CLI**: `npx react-native init` or the newer `create-expo-app` scaffolds a project; `npm`/`yarn` manage JavaScript dependencies. React Native projects are typically edited in VS Code.

Both frameworks still rely on Xcode and Android Studio behind the scenes to actually compile and run the app on iOS and Android respectively — the CLI just orchestrates them.

---

## 2.4 Simulators, Emulators, and Physical Devices

- An **iOS Simulator** and an **Android Emulator** are software that mimics a phone on your computer — fast to use during development, but they can't test everything (e.g., real camera hardware, GPS drift, battery behavior).
- Testing on a **physical device** connected via USB (or wirelessly) is essential before release, since performance, touch response, and sensors behave differently on real hardware.

## 2.5 Version Control

Regardless of platform, mobile projects are managed with **Git**, usually hosted on GitHub, GitLab, or Bitbucket. A `.gitignore` file is important for mobile projects specifically, since build folders, generated code, and IDE settings (like Xcode's `DerivedData` or Android's `build/` folder) should never be committed.

[Previous](./[1]-What-is-Mobile-Development.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[3]-Anatomy-of-a-Mobile-App-Project.md)
