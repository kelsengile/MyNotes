[Previous](./[30]-Introduction-to-iOS-Development.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[32]-Cross-Platform-Frameworks.md)

*Platform-Specific Development*

# Lesson 31 - Introduction to Android Development (Kotlin, Jetpack Compose/XML)

## 31.1 The Kotlin Language

**Kotlin** is Google's preferred language for Android development, replacing the older Java (which is still fully supported). Kotlin is concise, null-safe, and fully interoperable with existing Java code.

```kotlin
var username: String = "alex"
var age: Int? = null // nullable type - may hold null

age?.let {
    println("Age is $it")
} ?: println("Age is unknown")
```

Like Swift's optionals, Kotlin's `?` marks a type as **nullable**, and the compiler enforces null-checks before use, preventing `NullPointerException` crashes at compile time rather than runtime.

---

## 31.2 Jetpack Compose vs the XML View System

- **XML Views** — the original, imperative approach. Layouts are defined in `.xml` files and manipulated in code via `findViewById` or view binding.
- **Jetpack Compose** — Google's modern, declarative UI toolkit (analogous to SwiftUI). UI is described as Kotlin functions that automatically recompose when state changes.

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }

    Column {
        Text("Count: $count")
        Button(onClick = { count++ }) {
            Text("Increment")
        }
    }
}
```

Compose is now Google's recommended approach for new apps, though many existing codebases still rely heavily on the XML view system.

---

## 31.3 Android Project Structure

- **`AndroidManifest.xml`** — declares the app's components, required permissions, and metadata (the Android equivalent of iOS's `Info.plist`).
- **Activities and Fragments** — an `Activity` represents a single screen; `Fragment`s are reusable sub-screens that live inside an Activity. Compose apps often use a single Activity hosting all Composable screens.
- **`res/` folder** — holds resources like layouts, drawables (images), and string values, organized by resolution/language qualifiers (e.g. `res/drawable-hdpi/`, `res/values-es/`).
- **`build.gradle`** — the build configuration file(s) declaring dependencies, SDK versions, and build variants (using Gradle, Android's build system).

---

## 31.4 Android-Specific Concepts

- **Activity lifecycle** — Android calls methods like `onCreate`, `onStart`, `onResume`, `onPause`, and `onDestroy` as a screen becomes visible, loses focus, or is destroyed; apps must save/restore state around these transitions.
- **Intents** — the mechanism for navigating between screens or communicating with other apps (e.g. launching the camera app or sharing text).
- **Gradle** manages dependencies and build variants, similar in role to SPM/CocoaPods on iOS but also responsible for compiling and packaging the final app.

[Previous](./[30]-Introduction-to-iOS-Development.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[32]-Cross-Platform-Frameworks.md)
