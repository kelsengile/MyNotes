[Previous](./[6]-Functions-and-Methods.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[8]-Collections.md)

*Core Syntax*

# Lesson 7 - Classes, Objects & Object-Oriented Basics

## 7.1 Classes and Instances

A **class** is a blueprint describing properties (data) and methods (behavior). An **object** (or instance) is a specific thing created from that blueprint. Mobile apps model almost everything as objects — a `User`, a `Product`, a `Screen`.

```kotlin
class User(val name: String, var isOnline: Boolean) {
    fun greet(): String = "Hi, I'm $name"
}

val user = User("Alex", true)
println(user.greet())
```

---

## 7.2 Inheritance

A class can **inherit** from another, gaining its properties and methods while adding or overriding its own. Mobile UI frameworks built on inheritance (like UIKit and Android's classic View system) define a base `View`/`UIView` class, and every button, label, and layout is a subclass of it.

```swift
class Animal {
    func makeSound() { print("...") }
}
class Dog: Animal {
    override func makeSound() { print("Woof") }
}
```

Modern UI frameworks (SwiftUI, Jetpack Compose, Flutter) rely less on deep inheritance chains and more on **composition** — building complex UI by combining small, independent pieces — which tends to be easier to reason about and reuse. Both patterns are worth understanding since real codebases mix them.

---

## 7.3 Interfaces / Protocols

An **interface** (Kotlin/Dart) or **protocol** (Swift) defines a contract of methods a class promises to implement, without dictating how. This is how mobile frameworks let you plug custom behavior into their systems — for example, conforming to a `Codable` protocol (Swift) tells the framework your class can be converted to/from JSON.

```dart
abstract class Shape {
  double area();
}
class Circle implements Shape {
  double radius;
  Circle(this.radius);
  @override
  double area() => 3.14159 * radius * radius;
}
```

---

## 7.4 Structs vs Classes (Value vs Reference Types)

Swift and Dart distinguish between **value types** (`struct`, copied when assigned) and **reference types** (`class`, shared by reference when assigned). SwiftUI in particular encourages using structs for UI views and data models, since value types avoid a whole category of bugs caused by unexpectedly shared, mutated state. Kotlin doesn't have a separate `struct` keyword but achieves similar immutability with `data class` combined with `val` properties.

[Previous](./[6]-Functions-and-Methods.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[8]-Collections.md)
