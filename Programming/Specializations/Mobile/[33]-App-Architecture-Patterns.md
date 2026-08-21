[Previous](./[32]-Cross-Platform-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[34]-Dependency-Management.md)

*Architecture & Best Practices*

# Lesson 33 - App Architecture Patterns (MVC, MVVM, MVI)

## 33.1 Why Architecture Matters

As an app grows beyond a few screens, dumping all logic into your view code makes it hard to test, reuse, or reason about. Architecture patterns separate an app into layers with clear responsibilities: what to display, how to display it, and how to manage the underlying data/state.

---

## 33.2 MVC (Model-View-Controller)

The oldest and simplest pattern, used heavily in early UIKit apps:

- **Model** — the data and business logic.
- **View** — the visual UI elements.
- **Controller** — mediates between Model and View, responding to user input and updating the View.

MVC is easy to learn but tends to produce "massive view controllers" in practice, since the Controller often ends up handling too much — UI logic, business logic, and data fetching all at once.

---

## 33.3 MVVM (Model-View-ViewModel)

MVVM addresses MVC's bloat problem by introducing a **ViewModel** — an object that holds UI state and presentation logic, but has no direct reference to the View itself:

- **Model** — data and business logic (unchanged from MVC).
- **View** — purely displays what the ViewModel tells it to, and forwards user actions to the ViewModel.
- **ViewModel** — exposes observable state (e.g. a `@Published` property in SwiftUI, or a `StateFlow` in Kotlin) that the View automatically reflects.

```kotlin
class ProfileViewModel : ViewModel() {
    private val _username = MutableStateFlow("")
    val username: StateFlow<String> = _username

    fun loadUser() {
        _username.value = repository.getUsername()
    }
}
```

Because the View simply observes the ViewModel, MVVM works especially well with declarative UI frameworks like SwiftUI and Jetpack Compose, and makes the ViewModel easy to unit test in isolation from any UI.

---

## 33.4 MVI (Model-View-Intent)

MVI takes state management further by enforcing a strict **unidirectional data flow**:

1. The **View** emits **Intents** (user actions, e.g. "button tapped").
2. Intents are processed and produce a new immutable **State**.
3. The **View** re-renders entirely from that single State object.

This makes state changes fully predictable and easy to debug — the entire screen's state exists in one place at any moment — but it comes with more boilerplate than MVVM. MVI is popular in Compose/Flutter codebases that lean heavily into unidirectional, Redux-like state management.

---

## 33.5 Choosing a Pattern

There's no single "correct" architecture — MVC suits small apps or prototypes, MVVM is the common default for most production apps today, and MVI fits apps with complex, highly predictable state requirements. What matters most is consistency: pick one pattern and apply it uniformly across the app.

[Previous](./[32]-Cross-Platform-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[34]-Dependency-Management.md)
