[Previous](./[4]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[6]-Functions-and-Methods.md)

*Core Syntax*

# Lesson 5 - Control Flow: Conditionals & Loops

## 5.1 Conditionals

`if`/`else` statements branch your code based on a condition — for example, showing a "logged in" screen versus a "login" screen:

```dart
if (isLoggedIn) {
  showHomeScreen();
} else {
  showLoginScreen();
}
```

Mobile languages also offer a `switch` statement, useful for handling one of many discrete states (very common when reacting to network or UI states):

```swift
switch connectionState {
case .connected:
    print("Online")
case .connecting:
    print("Loading...")
case .disconnected:
    print("Offline")
}
```

Swift and Kotlin's `switch`/`when` require every possible case to be handled (exhaustiveness), which is a powerful safety feature — the compiler won't let you forget a state.

---

## 5.2 Loops

- **`for` loop**: iterate a known number of times or over a collection.
- **`while` loop**: repeat while a condition holds true.
- **`for-in`/`forEach`**: iterate directly over items in a list.

```kotlin
for (item in cartItems) {
    println(item.name)
}
```

In UI code, you rarely write manual loops to build screens — instead, frameworks provide list-building widgets (covered in Lesson 10) that iterate over data for you and only render what's visible on screen, which is far more efficient for long lists like a chat history or a product catalog.

---

## 5.3 Guard Clauses and Early Returns

Mobile codebases favor "fail fast" patterns to avoid deeply nested `if` blocks. Swift has a dedicated `guard` statement for this:

```swift
func processOrder(order: Order?) {
    guard let order = order else {
        print("No order to process")
        return
    }
    // order is safely unwrapped here
}
```

Kotlin and Dart achieve the same effect with an early `return` inside a plain `if`. This pattern keeps the "happy path" of a function unindented and easy to read.

[Previous](./[4]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[6]-Functions-and-Methods.md)
