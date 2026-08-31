[Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./%5B2%5D-Development-Environment.md)

*Getting Started*

# Lesson 1 - What is Mobile Development? Native vs Cross-Platform

## 1.1 What is Mobile Development?

Mobile development is the practice of building software applications that run on handheld devices like smartphones and tablets. Unlike a website, a mobile app is typically installed on the device, can be launched from a home screen icon, and can access hardware features such as the camera, GPS, and push notification systems. Mobile apps are built for two dominant operating systems: **iOS** (Apple's devices) and **Android** (Google's ecosystem, used by many manufacturers). Every approach to mobile development is ultimately a strategy for delivering an app to one or both of these platforms.

---

## 1.2 Native Development

"Native" development means writing an app specifically for one platform, using that platform's official language and tools.

- **iOS native**: written in **Swift** (or older Objective-C), using Apple's Xcode IDE and frameworks like SwiftUI or UIKit.
- **Android native**: written in **Kotlin** (or older Java), using Android Studio and frameworks like Jetpack Compose or the older XML-based View system.

Native apps get the best possible performance and the earliest access to new platform features, because they talk directly to the operating system's APIs. The tradeoff is that you must write and maintain two separate codebases — one for iOS, one for Android — which doubles the development and maintenance effort.

---

## 1.3 Cross-Platform Development

Cross-platform frameworks let you write one codebase that runs on both iOS and Android (and sometimes web or desktop too). The two most popular are:

- **Flutter** (by Google): uses the Dart language and draws its own UI pixel-by-pixel, so it looks and behaves identically on every platform.
- **React Native** (by Meta): uses JavaScript/TypeScript and React, and renders using each platform's real native UI components.

Cross-platform development trades a small amount of performance and platform-specific polish for a much faster development cycle, a single codebase, and a shared team skill set. Many companies choose cross-platform for most apps and drop down to native only when they need very specific device capabilities or maximum performance (e.g., games, AR).

---

## 1.4 Choosing an Approach

There's no single "correct" choice — it depends on your goals:

| Priority | Better Fit |
|---|---|
| Maximum performance, deep platform integration | Native |
| Fast development, one team, one codebase | Cross-platform |
| Small team or solo developer | Cross-platform |
| App with heavy graphics/games/AR | Native (or a game engine) |

This course covers concepts that apply broadly across both approaches — variables, UI layout, state, networking, and device features — while calling out native vs. cross-platform differences where they matter.

[Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./%5B2%5D-Development-Environment%20%282%29.md)
