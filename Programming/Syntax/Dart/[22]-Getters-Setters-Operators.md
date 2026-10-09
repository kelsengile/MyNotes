[Previous](./[21]-Constructors.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[23]-Inheritance.md)

*Object-Oriented Dart*

# Lesson 22 - Getters, Setters & Operators

Getters and setters let a class control how its data is read and changed while looking like plain fields to the code that uses it. Operator overloading lets your own classes work with symbols such as `+` and `==`. This lesson also covers the three methods almost every class should consider overriding: `toString`, `==`, and `hashCode`.

---

## 22.1 Computed Properties with `get`

A **getter** is a method that is accessed like a field (no parentheses). It is declared with the `get` keyword and returns a value, usually computed from other fields.

```dart
class Rectangle {
  final double width;
  final double height;

  Rectangle(this.width, this.height);

  double get area => width * height;
  double get perimeter => 2 * (width + height);
  bool get isSquare => width == height;
}

void main() {
  var r = Rectangle(4, 4);
  print(r.area);        // 16.0   (no parentheses)
  print(r.perimeter);   // 16.0
  print(r.isSquare);    // true
}
```

Why use a getter instead of a stored field?

- The value is always **up to date**; it is calculated each time from the current data.
- You avoid storing duplicate information that could become inconsistent.
- You can later change how the value is produced without changing the code that uses it.

Getters can have a block body when the logic is longer:

```dart
class Person {
  final String first;
  final String last;
  Person(this.first, this.last);

  String get fullName {
    return '$first $last';
  }

  String get initials => '${first[0]}${last[0]}';
}
```

Guidelines:

- Use a getter for something that **feels like a property** and is cheap to compute. If the work is expensive or has side effects, use a method (`calculateTotal()`).
- Every non-private field already provides an implicit getter, so you only write your own for computed values or to hide a private field:

```dart
class Account {
  double _balance = 0;
  double get balance => _balance;   // read-only access to a private field
}
```

- Getters can be `static` and can be declared in abstract classes and interfaces.

---

## 22.2 Custom `set` Logic

A **setter** is declared with `set`. It is called when you assign to the property, and lets you run code (validation, conversion, notifications) at that moment.

```dart
class Thermometer {
  double _celsius = 0;

  double get celsius => _celsius;

  set celsius(double value) {
    if (value < -273.15) {
      throw ArgumentError('Below absolute zero');
    }
    _celsius = value;
  }

  double get fahrenheit => _celsius * 9 / 5 + 32;

  set fahrenheit(double f) {
    celsius = (f - 32) * 5 / 9;   // reuses the validation above
  }
}

void main() {
  var t = Thermometer();
  t.celsius = 25;                   // calls the setter
  print(t.fahrenheit);              // 77.0

  t.fahrenheit = 212;
  print(t.celsius);                 // 100.0

  // t.celsius = -500;              // throws ArgumentError
}
```

Rules:

- A setter takes **exactly one parameter**.
- It is used with assignment syntax: `t.celsius = 25;`, never `t.celsius(25)`.
- If a getter and setter share a name, they together form one property. A getter **without** a setter is **read-only** (assigning gives an error).
- A setter does not return a value.

Because plain fields already behave like a getter/setter pair, you do not need to write them "just in case". Add a custom getter or setter only when you need extra behavior. You can always replace a public field with a getter/setter later without changing any calling code.

```dart
class Counter {
  int value = 0;   // Fine: simple and clear
}
```

---

## 22.3 Overloading Operators

Many operators are really methods with special names. By declaring `operator` methods you can make `+`, `-`, `[]`, `==`, and others work on your own classes.

```dart
class Vector {
  final double x;
  final double y;

  const Vector(this.x, this.y);

  Vector operator +(Vector other) => Vector(x + other.x, y + other.y);
  Vector operator -(Vector other) => Vector(x - other.x, y - other.y);
  Vector operator *(double scale) => Vector(x * scale, y * scale);
  Vector operator -() => Vector(-x, -y);   // unary minus

  @override
  String toString() => 'Vector($x, $y)';
}

void main() {
  var a = Vector(1, 2);
  var b = Vector(3, 4);

  print(a + b);      // Vector(4.0, 6.0)
  print(b - a);      // Vector(2.0, 2.0)
  print(a * 3);      // Vector(3.0, 6.0)
  print(-a);         // Vector(-1.0, -2.0)
}
```

### The index operators `[]` and `[]=`

```dart
class Grid {
  final List<int> _cells = List.filled(9, 0);

  int operator [](int index) => _cells[index];
  void operator []=(int index, int value) => _cells[index] = value;
}

void main() {
  var g = Grid();
  g[4] = 5;           // calls operator []=
  print(g[4]);        // 5  (calls operator [])
}
```

### Operators you can overload

| Category | Operators |
|---|---|
| Arithmetic | `+` `-` `*` `/` `~/` `%` and unary `-` |
| Comparison | `<` `>` `<=` `>=` and `==` |
| Bitwise / shift | `&` `\|` `^` `~` `<<` `>>` `>>>` |
| Index | `[]` `[]=` |

You **cannot** overload `!=` (it is always `!(a == b)`), `&&`, `||`, `!`, `??`, `=`, or the `.` and `..` operators. The compound assignments (`+=`, `-=`, ...) and `++`/`--` use your `+` and `-` automatically.

```dart
var v = Vector(1, 1);
v += Vector(2, 2);   // works because we defined +
```

Guideline: overload an operator **only when its meaning is obvious**. Adding two vectors with `+` is natural; making `+` do something surprising is not. Match the rules of the operator (for example `a + b` should not change `a`).

---

## 22.4 `toString`, `==`, and `hashCode`

Every class inherits three important members from `Object`. The defaults are usually not what you want for your own data classes.

### `toString()`

Used by `print` and string interpolation. The default prints `Instance of 'ClassName'`:

```dart
class Point {
  final int x, y;
  Point(this.x, this.y);

  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  print(Point(1, 2));          // Point(1, 2)
  print('At: ${Point(3, 4)}'); // At: Point(3, 4)
}
```

### `==` and `hashCode`

By default, `==` is **identity**: two objects are equal only if they are the very same object.

```dart
class Plain {
  final int x;
  Plain(this.x);
}

void main() {
  print(Plain(1) == Plain(1));   // false: different objects
}
```

To compare by **value**, override `==` **and** `hashCode` together. The rule: *if two objects are equal, they must have the same hash code.* Collections such as `Set` and `Map` rely on both.

```dart
class Point {
  final int x, y;
  const Point(this.x, this.y);

  @override
  bool operator ==(Object other) =>
      identical(this, other) ||
      other is Point && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);

  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  var a = Point(1, 2);
  var b = Point(1, 2);

  print(a == b);                 // true
  print(a.hashCode == b.hashCode); // true

  var set = {a, b, Point(3, 4)};
  print(set.length);             // 2: duplicates removed

  var names = {a: 'first'};
  print(names[Point(1, 2)]);     // first
}
```

Helpers for hash codes:

- `Object.hash(a, b, c)` combines several values (2 to 20 arguments).
- `Object.hashAll(iterable)` combines any number of values.
- `Object.hashAllUnordered(iterable)` for order-independent collections.

Overriding `==` and `hashCode` is explored further in Lesson 28.

---

## 22.5 Callable Classes (`call()`)

If a class defines a method named **`call`**, its objects can be invoked like functions, using the instance name followed by parentheses:

```dart
class Multiplier {
  final int factor;
  Multiplier(this.factor);

  int call(int value) => value * factor;
}

void main() {
  var triple = Multiplier(3);

  print(triple(5));          // 15  (calls triple.call(5))
  print(triple.call(5));     // 15  (the explicit form)
}
```

Such an object can carry **state** and be passed where a function is expected:

```dart
class Counter {
  int _count = 0;
  int call() => ++_count;
}

void main() {
  var next = Counter();
  print(next());   // 1
  print(next());   // 2
  print(next());   // 3

  var doubled = [1, 2, 3].map(Multiplier(2).call).toList();
  print(doubled);  // [2, 4, 6]
}
```

Another example, a configurable greeter:

```dart
class Greeter {
  final String greeting;
  const Greeter(this.greeting);

  String call(String name) => '$greeting, $name!';
}

void main() {
  const hello = Greeter('Hello');
  print(hello('Ana'));   // Hello, Ana!
}
```

When to use it:

- When a function needs **configuration or memory** (state) but should still be called like a function.
- The object is *not* of a function type (`Function`), but a callable object can be used with `.call` as a tear-off (`Multiplier(2).call`) when a real function is required.

For simple cases, a closure (Lesson 12) is usually shorter. Callable classes are a tool for when a closure is not enough.

---

[Previous](./[21]-Constructors.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[23]-Inheritance.md)