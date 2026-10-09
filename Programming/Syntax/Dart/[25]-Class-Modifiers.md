
[Previous](./[24]-Mixins.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[26]-Extensions.md)

*Object-Oriented Dart*

# Lesson 25 - Class Modifiers

By default, any class in Dart can be extended, implemented, or (if not abstract) instantiated by anyone. Dart 3 added **class modifiers** that let the author of a library say precisely what other code is allowed to do with their classes. This makes APIs safer and enables the compiler to check things it could not before.

> Class modifiers require Dart 3.0 or later.

---

## 25.1 Why Class Modifiers Exist

Without modifiers, every class is also an **interface** that others may implement, and may be inherited from. That creates problems for library authors:

- **Breaking changes:** If you add a method to a class, someone who `implements` it has now broken code, because they did not write the new method.
- **Broken assumptions:** If you assume nobody can extend your class (for example because it holds sensitive logic), you cannot enforce it.
- **No exhaustiveness:** The compiler cannot know all the subtypes of a class, so it cannot verify that a `switch` covers every case.

Class modifiers solve these by controlling four abilities:

| Ability | Meaning |
|---|---|
| **Construct** | Create instances: `Foo()` |
| **Extend** | `class Bar extends Foo` |
| **Implement** | `class Bar implements Foo` |
| **Mix in** | `class Bar with Foo` (for mixins) |

### An important detail: library boundaries

The restrictions apply **only outside the library that declares the class**. Within the same library (usually the same file), you can still do anything. This lets authors keep flexibility internally while limiting outsiders. The examples below assume the subclasses are in a *different* file than the class they are restricting.

### Quick overview

| Modifier | Can construct? | Extend? | Implement? |
|---|---|---|---|
| *(none)* | Yes | Yes | Yes |
| `abstract` | No | Yes | Yes |
| `base` | Yes | Yes (must stay `base`, `final`, or `sealed`) | **No** |
| `interface` | Yes | **No** | Yes |
| `final` | Yes | **No** | **No** |
| `sealed` | No | Only in same library | Only in same library |

---

## 25.2 `abstract`, `base`, `interface`, and `final`

### `abstract`

An `abstract` class cannot be instantiated and can declare members without bodies. This modifier existed before Dart 3 (Lesson 23):

```dart
abstract class Shape {
  double area();
}
```

### `base`

A `base` class can be **extended** but **not implemented**. This guarantees that every subtype inherits the real implementation, including the private members, so the class can rely on its own constructor logic and fields always being present.

```dart
// file: vehicle.dart
base class Vehicle {
  void moveForward(int meters) {
    // internal logic other code depends on
  }
}
```

```dart
// file: car.dart
import 'vehicle.dart';

base class Car extends Vehicle {}      // OK: extending, and marked base

// class Fake implements Vehicle {}    // ERROR: base classes can't be implemented
// class Truck extends Vehicle {}      // ERROR: subclass must be base, final, or sealed
```

The requirement that subtypes also be `base`, `final`, or `sealed` keeps the "no implementing" guarantee from being bypassed further down the hierarchy.

### `interface`

An `interface` class can be **implemented** but **not extended** (outside its library). It forces others to supply their own implementation, so they cannot depend on inherited code:

```dart
// file: logger.dart
interface class Logger {
  void log(String message) {}
}
```

```dart
// file: file_logger.dart
import 'logger.dart';

class FileLogger implements Logger {     // OK
  @override
  void log(String message) => print(message);
}

// class Other extends Logger {}         // ERROR: interface classes can't be extended
```

Use `interface class` for contracts where inheriting would be a mistake, or where you want to be free to change the class's internals.

### `final`

A `final` class **cannot be extended or implemented** outside its library. It can still be instantiated. This is the strictest option and gives you full control over the type hierarchy:

```dart
// file: token.dart
final class Token {
  final String value;
  Token(this.value);
}
```

```dart
// file: use.dart
import 'token.dart';

var t = Token('abc');                 // OK: constructing is allowed
// class FakeToken extends Token {}   // ERROR
// class FakeToken2 implements Token {} // ERROR
```

Many core Dart types are `final` in Dart 3 (for example `int`, `double`, `String`, and `Null`), which is why you cannot extend or implement them.

### Mixin modifiers

- `base mixin` is a mixin that cannot be implemented.
- Plain `mixin` declarations cannot be `final`, `interface`, or `sealed`... only `base` is available among these for mixin declarations.

---

## 25.3 `sealed` Classes and Exhaustive Switching

A **`sealed`** class is a special kind of abstract class whose **direct subtypes are all known** to the compiler, because they must be declared in the **same library**. Others cannot extend or implement it from outside.

Because the compiler knows every possible subtype, a `switch` over a sealed type can be checked for **exhaustiveness**: if you miss a subtype, you get an error.

```dart
sealed class Shape {}

class Circle extends Shape {
  final double radius;
  Circle(this.radius);
}

class Square extends Shape {
  final double side;
  Square(this.side);
}

class Triangle extends Shape {
  final double base, height;
  Triangle(this.base, this.height);
}

double area(Shape shape) => switch (shape) {
      Circle(radius: var r) => 3.14159 * r * r,
      Square(side: var s) => s * s,
      Triangle(base: var b, height: var h) => 0.5 * b * h,
    };

void main() {
  print(area(Circle(1)));        // 3.14159
  print(area(Square(3)));        // 9.0
  print(area(Triangle(4, 5)));   // 10.0
}
```

If you add `class Hexagon extends Shape {}` later, the `switch` in `area` becomes a **compile error** until you handle `Hexagon`. No `default` case is needed or wanted.

Properties of `sealed` classes:

- A sealed class is implicitly **abstract**: `Shape()` cannot be instantiated.
- Its subtypes may be in the same library only. In a multi-file library, use `part` files (Lesson 36).
- The subtypes themselves are free to be normal classes, or can be further restricted (for example `final class Circle extends Shape`).
- Sealed hierarchies are great for modeling **a value that is exactly one of several kinds**: states, events, results, and syntax trees.

### A typical use: states

```dart
sealed class LoadState {}

class Loading extends LoadState {}

class Loaded extends LoadState {
  final List<String> items;
  Loaded(this.items);
}

class Failed extends LoadState {
  final String error;
  Failed(this.error);
}

String render(LoadState state) => switch (state) {
      Loading() => 'Please wait...',
      Loaded(items: var list) => 'Got ${list.length} items',
      Failed(error: var e) => 'Error: $e',
    };
```

Sealed classes are used again for result-style error handling in Lesson 30.

---

## 25.4 Combining Modifiers

Modifiers can be combined to build layered restrictions. A class declaration may contain, **in this order**:

1. optionally `abstract`,
2. optionally one of `base`, `interface`, or `final`,
3. optionally `mixin`,
4. then `class`.

Common and useful combinations:

| Declaration | Meaning |
|---|---|
| `abstract base class` | Not instantiable, and can only be extended (never implemented) |
| `abstract interface class` | Not instantiable and can only be implemented: a pure contract |
| `abstract final class` | Not instantiable, extendable, or implementable outside the library: usually a holder for static members |
| `base mixin class` | A mixin class that can be extended or mixed in, but not implemented |
| `abstract mixin class` | An abstract class that may also be used as a mixin |

```dart
// A pure contract: others implement it, nobody can extend it, nobody can instantiate it
abstract interface class Repository {
  Future<String> load(String id);
  Future<void> save(String id, String data);
}

// A namespace-like holder that cannot be instantiated or extended
abstract final class AppConfig {
  static const String name = 'MyApp';
  static const int version = 3;
}
```

### Combinations that are not allowed

- `sealed` already implies `abstract` and the library restriction, so it **cannot** be combined with `abstract`, `base`, `interface`, or `final`.
- `mixin class` cannot be combined with `sealed`, `interface`, or `final`.
- `abstract` and `final`... *can* be combined (`abstract final class`) because they restrict different things.

### Choosing a modifier

| If you want to... | Use |
|---|---|
| Allow everything (the default) | no modifier |
| Force subclasses to implement members | `abstract` |
| Guarantee all subtypes inherit your code | `base` |
| Allow only implementations (a contract) | `interface` / `abstract interface` |
| Lock the class down completely | `final` |
| Model "exactly one of these known subtypes" with exhaustive switches | `sealed` |

For application code in a single package, the default (no modifier) and `sealed` are the most common. Library and package authors use `base`, `interface`, and `final` to protect their APIs.

---

[Previous](./[24]-Mixins.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[26]-Extensions.md)