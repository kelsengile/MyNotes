[Previous](./[3]-Anatomy-of-a-Mobile-App-Project.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[5]-Control-Flow.md)

*Core Syntax*

# Lesson 4 - Variables, Data Types & Operators

## 4.1 Declaring Variables

A variable stores a value your app can read or change later. Mobile languages generally prefer **type safety** — the compiler knows a variable's type ahead of time, which catches bugs early.

```swift
// Swift
var score = 10        // mutable
let maxScore = 100     // constant
```

```kotlin
// Kotlin
var score = 10        // mutable
val maxScore = 100     // constant
```

```dart
// Dart (Flutter)
var score = 10;
final maxScore = 100;
```

Notice the pattern across languages: a **mutable** keyword (`var`) for values that change, and a **constant/immutable** keyword (`let`, `val`, `final`) for values that shouldn't. Preferring constants where possible reduces bugs, since the compiler will stop you from accidentally reassigning them.

---

## 4.2 Common Data Types

Every mobile language has a similar core set of primitive types:

- **Integer** (`Int`) — whole numbers, e.g., `42`.
- **Floating-point/Double** (`Double`, `Float`) — decimal numbers, e.g., `3.14`.
- **Boolean** (`Bool`, `Boolean`) — `true` or `false`.
- **String** — text, e.g., `"Hello"`.
- **Nullable/Optional types** — a value that might be absent. Swift uses `String?`, Kotlin uses `String?`, Dart uses `String?`. Handling "nothing is there" safely is a first-class concern in mobile languages because network calls and user input frequently return no value.

---

## 4.3 Operators

Arithmetic (`+ - * / %`), comparison (`== != < > <= >=`), and logical (`&& || !`) operators work largely as you'd expect from other programming languages. One mobile-specific operator worth knowing early is the **null-coalescing / safe-call** operator, used to safely work with optional values without crashing:

```swift
let name: String? = nil
print(name ?? "Guest")   // prints "Guest" if name is nil
```

```kotlin
val name: String? = null
println(name ?: "Guest")
```

```dart
String? name;
print(name ?? "Guest");
```

---

## 4.4 Type Inference vs Explicit Types

All three languages support **type inference** (the compiler figures out the type from the assigned value), but you can also declare types explicitly, which is often clearer in function signatures and class properties:

```dart
int score = 10;          // explicit
var score = 10;          // inferred, still strongly typed
```

Explicit typing is especially useful for function parameters and return values, since it documents what a function expects without needing extra comments.

[Previous](./[3]-Anatomy-of-a-Mobile-App-Project.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[5]-Control-Flow.md)
