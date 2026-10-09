
[Previous](./[20]-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[22]-Getters-Setters-Operators.md)

*Object-Oriented Dart*

# Lesson 21 - Constructors

A **constructor** is a special function that creates and sets up a new object. Dart offers more kinds of constructors than most languages, each designed for a specific job. Mastering them makes your classes safer and shorter.

---

## 21.1 Default and Generative Constructors

### The default constructor

If you do not write a constructor, Dart gives your class a **default constructor** with no parameters:

```dart
class Empty {}

void main() {
  var e = Empty();   // calls the implicit default constructor
}
```

### Generative constructors

A **generative constructor** creates a new instance. It has the same name as the class:

```dart
class Point {
  double x;
  double y;

  Point(double x, double y)
      : x = x,
        y = y;
}
```

Here the part after the colon is an *initializer list* (see 21.4). Because assigning parameters to fields is so common, Dart offers a shortcut (next section). The longhand body style also exists:

```dart
class Person {
  late String name;
  late int age;

  Person(String name, int age) {
    this.name = name;   // `this` separates the field from the parameter
    this.age = age;
  }
}
```

Important rules:

- Non-nullable fields **must be initialized before the constructor body runs** (unless they are `late`). That is why Dart prefers initializer lists and `this.x` parameters over assigning inside the body.
- A class can have **only one unnamed constructor** (the one with the class's name). For more, use named constructors (21.3).
- **Constructors are not inherited** from a superclass.
- The order of execution is: initializer list, then the superclass constructor, then the constructor body.

---

## 21.2 Initializing Formal Parameters (`this.x`)

Writing `this.x` directly in the parameter list assigns the argument to the field automatically:

```dart
class Point {
  final double x;
  final double y;

  Point(this.x, this.y);   // that is the whole constructor
}

void main() {
  var p = Point(3, 4);
  print('${p.x}, ${p.y}');   // 3.0, 4.0
}
```

This works with every parameter style:

```dart
class User {
  final String name;
  final int age;
  final String? email;

  User(this.name, {required this.age, this.email});   // positional + named
}

class Config {
  final int retries;
  final bool verbose;

  Config({this.retries = 3, this.verbose = false});   // named with defaults
}

void main() {
  var u = User('Ana', age: 21);
  var c = Config(verbose: true);
  print('${u.name} ${u.age} ${u.email}');   // Ana 21 null
  print('${c.retries} ${c.verbose}');       // 3 true
}
```

Notes:

- The parameter keeps the **field's type**, so you do not repeat it.
- A **private** field cannot be set through a *named* parameter, because the name of a named parameter cannot start with an underscore. Use a positional parameter, or an initializer list:

```dart
class Account {
  final double _balance;

  Account(this._balance);                      // OK: positional
  Account.named({required double balance})     // OK: initializer list
      : _balance = balance;
}
```

---

## 21.3 Named Constructors

A **named constructor** has the form `ClassName.name(...)`. It lets one class offer several clear ways to be created, and the name documents the intent:

```dart
class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  Point.origin()
      : x = 0,
        y = 0;

  Point.fromList(List<double> values)
      : x = values[0],
        y = values[1];

  Point.polar(double radius, double angle)
      : x = radius * _cos(angle),
        y = radius * _sin(angle);
}
```

(`_cos` and `_sin` stand for any functions; in real code you would use `cos` and `sin` from `dart:math`.)

```dart
void main() {
  var a = Point(1, 2);
  var b = Point.origin();
  var c = Point.fromList([5, 6]);
  print('${b.x}, ${b.y}');   // 0.0, 0.0
  print('${c.x}, ${c.y}');   // 5.0, 6.0
}
```

The standard library uses this everywhere: `List.filled(...)`, `List.generate(...)`, `DateTime.now()`, `Duration(...)`, `Uri.parse(...)` (that last one is a static method, but the idea is the same).

Remember that named constructors are **not inherited**; a subclass that wants one must declare its own.

---

## 21.4 Initializer Lists

An **initializer list** runs **before** the constructor body. It comes after a colon and contains comma-separated items. Use it to:

1. Initialize `final` fields from computed values.
2. Run `assert` checks.
3. Call the superclass constructor (21.8).

```dart
class Rectangle {
  final double width;
  final double height;
  final double area;

  Rectangle(double w, double h)
      : assert(w > 0 && h > 0, 'sides must be positive'),
        width = w,
        height = h,
        area = w * h;
}

void main() {
  var r = Rectangle(3, 4);
  print(r.area);   // 12.0
}
```

Another very common use: initializing from a map (JSON-style data):

```dart
class User {
  final String name;
  final int age;

  User.fromMap(Map<String, dynamic> map)
      : name = map['name'] as String,
        age = map['age'] as int;
}

void main() {
  var u = User.fromMap({'name': 'Ana', 'age': 21});
  print('${u.name}, ${u.age}');   // Ana, 21
}
```

Rules:

- The right-hand side of an initializer **cannot use `this`** (the object does not exist yet), though it can use the constructor's parameters.
- Assertions in initializer lists run only in debug mode (Lesson 30).
- You may mix `this.x` parameters and initializer-list entries.

---

## 21.5 Redirecting Constructors

A **redirecting constructor** has no body of its own. It just calls another constructor of the same class using `this`, after a colon:

```dart
class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  // Redirects to the main constructor
  Point.alongXAxis(double x) : this(x, 0);
  Point.alongYAxis(double y) : this(0, y);
  Point.origin() : this(0, 0);
}

void main() {
  var p = Point.alongXAxis(5);
  print('${p.x}, ${p.y}');   // 5.0, 0.0
}
```

This keeps the real initialization logic in **one** place. A redirecting constructor's body is empty (you may only write `;`), and it can't have an initializer list besides the redirect.

---

## 21.6 `const` Constructors

If a class produces objects that **never change**, you can give it a `const` constructor. Then you can create compile-time constant objects, which Dart shares in memory.

Requirements:

- Every field must be `final`.
- The constructor is marked `const`.

```dart
class Point {
  final double x;
  final double y;

  const Point(this.x, this.y);
}

void main() {
  var a = const Point(1, 2);
  var b = const Point(1, 2);

  print(identical(a, b));   // true: the same single object

  var c = Point(1, 2);      // without `const`: a normal run-time object
  var d = Point(1, 2);
  print(identical(c, d));   // false
}
```

Rules and tips:

- Using the `const` keyword when creating the object is what makes it a constant. A `const` constructor called without `const` makes a normal object.
- Inside a `const` context (such as a `const` list), the inner `const` can be omitted:

```dart
const points = [Point(0, 0), Point(1, 1)];   // each Point is const automatically
```

- All arguments to a `const` constructor must themselves be constants.
- `const` constructors are heavily used in Flutter, because constant widgets are reused instead of rebuilt.

---

## 21.7 Factory Constructors

A **factory constructor** is declared with the `factory` keyword. Unlike a normal constructor, it is **not required to create a new object**. It runs ordinary code and **returns** an instance (which can be an existing one, or even an instance of a subclass).

### Returning a cached instance

```dart
class Logger {
  final String name;
  static final Map<String, Logger> _cache = {};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  Logger._internal(this.name);    // private named constructor

  void log(String message) => print('[$name] $message');
}

void main() {
  var a = Logger('app');
  var b = Logger('app');
  print(identical(a, b));   // true: the same object was returned
  a.log('started');         // [app] started
}
```

Notice the **private constructor** `Logger._internal` (private because of the underscore). It is the only way to truly build a new object, and only code in the file can call it.

### Returning a subtype

```dart
abstract class Shape {
  double get area;

  factory Shape(String kind, double size) {
    switch (kind) {
      case 'square':
        return Square(size);
      case 'circle':
        return Circle(size);
      default:
        throw ArgumentError('Unknown shape: $kind');
    }
  }
}

class Square implements Shape {
  final double side;
  Square(this.side);
  @override
  double get area => side * side;
}

class Circle implements Shape {
  final double radius;
  Circle(this.radius);
  @override
  double get area => 3.14159 * radius * radius;
}
```

### Parsing data (`fromJson`)

Factory constructors are often used to build objects from other data, especially when validation or choosing between options is needed:

```dart
class Temperature {
  final double celsius;
  Temperature._(this.celsius);

  factory Temperature.fromFahrenheit(double f) =>
      Temperature._((f - 32) * 5 / 9);

  factory Temperature.parse(String text) {
    var value = double.tryParse(text);
    if (value == null) throw FormatException('Not a number', text);
    return Temperature._(value);
  }
}
```

### Key facts

| | Generative constructor | Factory constructor |
|---|---|---|
| Creates a new object each time | Always | Not necessarily |
| Can return a subtype or cached object | No | **Yes** |
| Can use `this` / initializer list | Yes (initializer list) | **No** |
| Can be `const` | Yes | Only as a redirecting factory to a const constructor |

---

## 21.8 Super Parameters

When a subclass constructor passes values straight to its superclass constructor, you can use **super parameters** (Dart 2.17 and later) instead of repeating the parameters. Writing `super.name` forwards the argument to the matching superclass parameter:

```dart
class Animal {
  final String name;
  final int age;

  Animal(this.name, this.age);
}

// Without super parameters
class DogOld extends Animal {
  final String breed;

  DogOld(String name, int age, this.breed) : super(name, age);
}

// With super parameters: shorter
class Dog extends Animal {
  final String breed;

  Dog(super.name, super.age, this.breed);
}

void main() {
  var d = Dog('Rex', 3, 'Beagle');
  print('${d.name} ${d.age} ${d.breed}');   // Rex 3 Beagle
}
```

### With named parameters

```dart
class Vehicle {
  final String brand;
  final int wheels;

  Vehicle({required this.brand, this.wheels = 4});
}

class Motorcycle extends Vehicle {
  final bool hasSidecar;

  Motorcycle({required super.brand, this.hasSidecar = false}) : super(wheels: 2);
}

void main() {
  var m = Motorcycle(brand: 'Honda');
  print('${m.brand} ${m.wheels} ${m.hasSidecar}');   // Honda 2 false
}
```

Rules:

- Positional super parameters must be in the same order and position as the superclass's positional parameters.
- A subclass constructor may also call `super(...)` explicitly for the remaining arguments, but you cannot forward the same parameter both ways.
- If you do not call a superclass constructor, Dart implicitly calls the superclass's **unnamed, no-argument** constructor (it must exist, or you get an error).

Inheritance itself is covered in Lesson 23.

---

[Previous](./[20]-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[22]-Getters-Setters-Operators.md)