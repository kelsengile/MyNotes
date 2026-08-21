[Previous](./[33]-App-Architecture-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[35]-Testing-Mobile-Apps.md)

*Architecture & Best Practices*

# Lesson 34 - Dependency Management & Package Managers

## 34.1 What is a Dependency?

A **dependency** is external code your app relies on — a library or framework someone else wrote, that you include instead of writing that functionality yourself (e.g. a networking library, an image-loading library, an analytics SDK). A **package manager** automates downloading, versioning, and linking these dependencies into your project.

---

## 34.2 iOS Package Managers

- **Swift Package Manager (SPM)** — Apple's native solution, built into Xcode. You add a package by URL, and Xcode resolves and downloads it automatically.
- **CocoaPods** — an older, widely-used third-party tool. Dependencies are declared in a `Podfile` and installed via `pod install`, which generates an `.xcworkspace` file to open instead of the `.xcodeproj`.

```ruby
# Podfile
platform :ios, '15.0'
target 'MyApp' do
  pod 'Alamofire', '~> 5.8'
end
```

---

## 34.3 Android Package Managers

Android uses **Gradle** as both its build system and dependency manager. Dependencies are declared in the module's `build.gradle` (or `build.gradle.kts`) file and pulled automatically from repositories like Maven Central or Google's Maven repo.

```kotlin
// build.gradle.kts
dependencies {
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("io.coil-kt:coil-compose:2.5.0")
}
```

---

## 34.4 Cross-Platform Package Managers

- **Flutter** uses **pub**, with dependencies declared in `pubspec.yaml` and installed via `flutter pub get`.
- **React Native** uses **npm** or **yarn**, the same package managers used in general JavaScript development, with dependencies declared in `package.json`.

---

## 34.5 Semantic Versioning

Most package managers follow **semantic versioning** (`MAJOR.MINOR.PATCH`, e.g. `5.8.2`):

- **MAJOR** — breaking changes that may require code updates.
- **MINOR** — new features, backward-compatible.
- **PATCH** — bug fixes, backward-compatible.

Version constraints like `~> 5.8` (CocoaPods) or `^2.5.0` (npm) let you automatically accept safe updates (minor/patch) while avoiding breaking major version bumps until you're ready to test them.

[Previous](./[33]-App-Architecture-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[35]-Testing-Mobile-Apps.md)
