[Previous](./[4]-Comments-and-Documentation.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[6]-Numbers-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables & Type Inference

Variables are how programs remember things. Dart is a **statically typed** language, meaning every variable has a type known before the program runs, but it is also smart enough to figure out most types for you. This lesson covers every way to declare a variable and when to use each.

---

## 5.1 What Is a Variable?

A **variable** is a named storage location that holds a value. You create one by *declaring* it and (usually) giving it an initial value:

```dart
void main() {
  int age = 25;
  String name = 'Maria';
  double height = 1.62;
  bool isStudent = true;

  print('$name is $age years old.');
}
```

Each variable has:

- a **type** (`int`, `String`, ...), which decides what values it can hold,
- a **name** (`age`, `name`, ...), and
- a **value**.

Because Dart is type-safe, you cannot put the wrong kind of value in a variable:

```dart
int age = 25;
age = 'twenty-five'; // ERROR: A value of type 'String' can't be assigned to 'int'
```

The error appears in your editor *before* you run the program, which is one of the biggest benefits of static typing.

---

## 5.2 Declaring with `var` and Explicit Types

You can write the type yourself (an **explicit type**), or use `var` and let Dart **infer** it from the initial value:

```dart
void main() {
  String city = 'Manila';   // explicit
  var country = 'Philippines'; // inferred as String
  var year = 2026;             // inferred as int
  var price = 9.99;            // inferred as double
  var items = [1, 2, 3];       // inferred as List<int>
}
```

Inference happens **once**, at declaration. After that the type is fixed:

```dart
var count = 10;   // count is an int
count = 20;       // OK
count = 'many';   // ERROR: String can't be assigned to int
```

You can check a variable's type at runtime with `runtimeType`:

```dart
var x = 3.5;
print(x.runtimeType); // double
```

**Default values:** A variable of a *nullable* type (one that ends in `?`, see Lesson 8) that is not given a value starts as `null`:

```dart
int? score;
print(score); // null
```

A non-nullable variable must be assigned before you read it. The analyzer checks this:

```dart
int total;
// print(total);  // ERROR: 'total' must be assigned before it can be used
total = 5;
print(total);     // OK
```

**Which style should you use?** The Dart style guide recommends `var` (or `final`) for local variables when the type is obvious from the right-hand side, and an explicit type when it helps readability or when there is no initial value:

```dart
var message = 'Hello';           // obvious
List<String> names = [];         // explicit type on an empty list
var names2 = <String>[];         // or put the type on the literal
```

One important exception: `var` with **no initializer** produces a `dynamic` variable (see 5.5), so give it an explicit type in that case:

```dart
var anything;       // dynamic: avoid
String text;        // better: explicit type, assigned later
```

---

## 5.3 `final` vs `const`

Both keywords create variables that can be assigned **only once**. The difference is *when* the value must be known.

### `final`: set once, known at run time

```dart
void main() {
  final name = 'Ana';
  // name = 'Ben'; // ERROR: a final variable can only be set once

  final now = DateTime.now(); // OK: value is computed when the program runs
  print(now);
}
```

### `const`: a compile-time constant

A `const` value must be fully known **when the code is compiled**, so it can only use literals and other constants:

```dart
const pi2 = 3.14159 * 2;      // OK: computed from literals
const appName = 'Notes';      // OK
const seconds = 60 * 60;      // OK

// const now = DateTime.now(); // ERROR: not a compile-time constant
```

`const` also applies to values like lists, sets, maps, and objects. A `const` collection is **deeply immutable** and **canonicalized**, meaning identical const values share the same object in memory:

```dart
void main() {
  const a = [1, 2, 3];
  const b = [1, 2, 3];
  print(identical(a, b)); // true: same object

  // a.add(4); // Runtime error: cannot modify an unmodifiable list
}
```

### The key difference with collections

`final` stops you from *reassigning the variable*, but the object it points to may still change. `const` makes the object itself unchangeable:

```dart
final fruits = ['apple', 'banana'];
fruits.add('cherry');       // OK: the list is mutable
// fruits = ['kiwi'];       // ERROR: the variable is final

const colors = ['red', 'green'];
// colors.add('blue');      // Runtime error: the list is unmodifiable
```

### Quick guide

| Keyword | Assign again? | Value known at compile time? | Typical use |
|---|---|---|---|
| `var` | Yes | Not required | Values that change |
| `final` | No | Not required | Values set once while running |
| `const` | No | **Required** | Fixed values such as configuration and literals |

**Rule of thumb:** use `final` by default, `const` when the value is truly constant, and `var` only when the variable must change. (You can also write `const` on class fields using `static const`, covered in the classes lesson.)

---

## 5.4 `late` Variables and Lazy Initialization

Normally Dart insists that a non-nullable variable has a value right away. `late` tells the compiler, "I promise to assign this before I read it."

### Declaring now, assigning later

```dart
late String description;

void main() {
  description = 'Set later, before it is used';
  print(description);
}
```

If you read a `late` variable before assigning it, the program throws a `LateInitializationError` at run time. The promise is yours to keep.

### Lazy initialization

If a `late` variable has an initializer, that initializer runs **only the first time the variable is read**, not when it is declared:

```dart
String readConfig() {
  print('Reading config...');
  return 'config-data';
}

late final String config = readConfig();

void main() {
  print('Program started');
  print(config); // "Reading config..." appears here, on first use
  print(config); // already computed; readConfig() does not run again
}
```

Output:

```
Program started
Reading config...
config-data
config-data
```

This is useful for expensive work that might never be needed.

### `late final`

Combining `late` and `final` creates a variable that is assigned **exactly once**, but not necessarily at its declaration:

```dart
late final int id;

void main() {
  id = 7;    // OK, first assignment
  // id = 8; // Runtime error: already assigned
}
```

---

## 5.5 `dynamic` vs `Object`

Two types can hold a value of *any* type, and they behave very differently.

### `Object`

`Object` is the root of all non-nullable types. A variable of type `Object` can hold anything (except `null`), but the compiler only lets you use members that every object has:

```dart
Object value = 'hello';
print(value.toString());   // OK: every Object has toString()
// print(value.length);    // ERROR: 'length' isn't defined for Object
```

To use something more specific, you must check the type first:

```dart
if (value is String) {
  print(value.length); // OK: value is promoted to String here
}
```

(To also allow `null`, use `Object?`.)

### `dynamic`

`dynamic` turns **off static type checking** for that variable. Any member can be used, and mistakes are only found when the program runs:

```dart
dynamic data = 'hello';
print(data.length);  // 5, works because data is currently a String

data = 42;
print(data.length);  // compiles fine, but CRASHES at run time (int has no 'length')
```

### Which should you use?

| | `Object` / `Object?` | `dynamic` |
|---|---|---|
| Type checking | Compile time | None (run time only) |
| Safe to call arbitrary members | No | Yes, but may crash |
| Recommended | **Yes**, when you need "any type" | Rarely: JSON handling, interop |

Prefer `Object?` over `dynamic` whenever you can. If you truly need `dynamic` (for instance, decoded JSON, covered later), keep its use small and convert to real types quickly.

---

## 5.6 Naming Conventions

Dart has widely followed naming rules, and the analyzer's lint rules check many of them.

| Kind of name | Style | Examples |
|---|---|---|
| Variables, functions, parameters, constants | `lowerCamelCase` | `userName`, `calculateTotal`, `maxRetries` |
| Classes, enums, typedefs, extensions | `UpperCamelCase` | `ShoppingCart`, `HttpClient` |
| Files, folders, packages, libraries | `lowercase_with_underscores` | `shopping_cart.dart`, `my_app` |
| Import prefixes | `lowercase_with_underscores` | `import 'dart:math' as math;` |
| Private names | start with `_` | `_internalCounter` |

Notes:

- Even `const` values use `lowerCamelCase` in Dart (`const maxItems = 10;`), not `ALL_CAPS`.
- Acronyms longer than two letters are written like words: `HttpRequest`, `Uri`, not `HTTPRequest`.
- Names are **case-sensitive**: `total` and `Total` are different variables.
- A name may contain letters, digits, `_`, and `$`, but cannot start with a digit and cannot be a reserved word such as `class` or `if`.
- Choose descriptive names: `elapsedSeconds` is better than `t`.

---

[Previous](./[4]-Comments-and-Documentation.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[6]-Numbers-and-Booleans.md)
