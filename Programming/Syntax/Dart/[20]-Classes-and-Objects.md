[Previous](./[19]-Typedefs.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[21]-Constructors.md)

*Object-Oriented Dart*

# Lesson 20 - Classes & Objects

So far you have worked with built-in types. A **class** lets you design your own types. It is a blueprint that describes what data something holds (its **fields**) and what it can do (its **methods**). An **object** is one concrete thing built from that blueprint. Dart is object-oriented: nearly everything, including numbers and functions, is an object.

---

## 20.1 Declaring a Class

Use the `class` keyword and a name in `UpperCamelCase`:

```dart
class Dog {
  String name = 'Rex';
  int age = 3;

  void bark() {
    print('$name says Woof!');
  }
}
```

To create an object (an **instance**) call the class name like a function. The keyword `new` is optional and normally left out:

```dart
void main() {
  var dog = Dog();       // create an instance
  dog.bark();            // Rex says Woof!

  var another = Dog();   // a second, independent object
  another.name = 'Luna';
  another.bark();        // Luna says Woof!
  dog.bark();            // Rex says Woof! (unchanged)
}
```

Each object has its own copy of the fields. The class is the pattern; the objects are the things built from it.

Useful facts:

- Every class implicitly extends `Object` and inherits members such as `toString()`, `==`, `hashCode`, and `runtimeType`.
- A class with no constructor gets a default no-argument constructor (constructors are covered in Lesson 21).
- Classes can be declared at the top level of a file only (not inside functions).
- You can print the type of an object with `runtimeType`:

```dart
print(Dog().runtimeType);   // Dog
```

---

## 20.2 Instance Variables and Methods

### Instance variables (fields)

Fields hold the state of an object. A field's rules are the same as for variables:

```dart
class Product {
  String name = 'Unnamed';         // has a default value
  double price = 0;                // has a default value
  String? description;             // nullable: starts as null
  late String sku;                 // will be assigned before use
  final DateTime created = DateTime.now();   // set once
}
```

A non-nullable field must be given a value, either with an initializer as above or through a constructor (next lesson). Otherwise the analyzer reports an error.

Fields are accessed with the dot (`.`) operator. Each field automatically gets a **getter**, and non-`final` fields also get a **setter**:

```dart
void main() {
  var p = Product();
  p.name = 'Laptop';
  p.price = 999.99;
  p.sku = 'LT-01';
  print('${p.name} costs ${p.price}');   // Laptop costs 999.99
  // p.created = DateTime.now();         // ERROR: final field
}
```

### Methods

A method is a function that belongs to a class and can use the object's fields:

```dart
class Rectangle {
  double width = 1;
  double height = 1;

  double area() {
    return width * height;
  }

  double perimeter() => 2 * (width + height);

  void scale(double factor) {
    width *= factor;
    height *= factor;
  }
}

void main() {
  var r = Rectangle()
    ..width = 4
    ..height = 3;

  print(r.area());        // 12.0
  print(r.perimeter());   // 14.0

  r.scale(2);
  print(r.area());        // 48.0
}
```

Methods can take parameters (positional, named, optional), return values, and call other methods of the same object. The cascade operator (`..`) from Lesson 9 is handy when configuring a new object.

### `toString()`

`print(object)` calls `toString()`. The default output is not very useful (`Instance of 'Rectangle'`), so classes often override it:

```dart
class Person {
  String name = '';
  int age = 0;

  @override
  String toString() => 'Person($name, $age)';
}

void main() {
  var p = Person()
    ..name = 'Ana'
    ..age = 21;
  print(p);   // Person(Ana, 21)
}
```

---

## 20.3 `this` and Instance Access

Inside a method, **`this`** refers to the object the method was called on. Normally you do not need to write it, because Dart finds the field automatically:

```dart
class Counter {
  int count = 0;

  void increment() {
    count++;          // same as this.count++
  }
}
```

Use `this` when a **local name hides a field** (a parameter with the same name):

```dart
class Counter {
  int count = 0;

  void setCount(int count) {
    this.count = count;   // field on the left, parameter on the right
  }
}
```

The Dart style guide says to omit `this` except in cases like this (the `unnecessary_this` lint checks it).

You can also use `this` to pass the current object to another function, or to return it so calls can be chained:

```dart
class Builder {
  final _parts = <String>[];

  Builder add(String part) {
    _parts.add(part);
    return this;            // return the same object
  }

  String build() => _parts.join(' ');
}

void main() {
  var text = Builder().add('Hello').add('Dart').add('World').build();
  print(text);   // Hello Dart World
}
```

Each object keeps its own state. Two objects of the same class are independent unless you deliberately point two variables at the same object:

```dart
void main() {
  var a = Counter();
  var b = a;           // b refers to the SAME object as a
  b.increment();
  print(a.count);      // 1

  var c = Counter();   // a different object
  print(c.count);      // 0
}
```

---

## 20.4 Static Members

Normally each object has its own fields. A **static** member belongs to the **class itself**, shared by all instances, and is accessed through the class name.

### Static variables

```dart
class Student {
  static int totalStudents = 0;   // one value shared by the whole class
  String name;

  Student(this.name) {
    totalStudents++;
  }
}

void main() {
  Student('Ana');
  Student('Ben');
  print(Student.totalStudents);   // 2
}
```

### Static constants

Use `static const` for fixed values related to the class:

```dart
class Circle {
  static const double pi = 3.14159;
  final double radius;
  Circle(this.radius);

  double get area => pi * radius * radius;
}

void main() {
  print(Circle.pi);              // 3.14159
  print(Circle(2).area);         // 12.56636
}
```

### Static methods

A static method does not need an object. It **cannot use `this` or instance fields**, because there is no instance:

```dart
class MathUtils {
  static int square(int n) => n * n;
  static int max3(int a, int b, int c) => [a, b, c].reduce((x, y) => x > y ? x : y);
}

void main() {
  print(MathUtils.square(5));    // 25
  print(MathUtils.max3(4, 9, 2)); // 9
}
```

Details to remember:

- Static variables are **lazily initialized**: their initializer runs the first time they are read.
- You access statics as `ClassName.member`, never through an instance.
- The style guide suggests preferring **top-level functions and constants** over classes that exist only to hold static members (like `MathUtils` above); a plain `int square(int n) => n * n;` in a library is simpler. Statics are most useful when closely tied to the class, such as named creators or class-wide constants.

---

## 20.5 Privacy and Libraries (`_` prefix)

Dart has no `public`, `private`, or `protected` keywords. Instead:

- Names are **public** by default.
- A name that starts with an underscore `_` is **private to its library**.

A **library** is usually one `.dart` file. So "private" means "not visible from other files", not "not visible from other classes in the same file".

```dart
// file: bank_account.dart
class BankAccount {
  double _balance = 0;            // private field

  double get balance => _balance; // public read-only access

  void deposit(double amount) {
    if (amount <= 0) return;
    _balance += amount;
  }

  void _audit() {                 // private method
    print('Balance is $_balance');
  }

  void close() => _audit();       // OK: used inside the same file
}
```

```dart
// file: main.dart
import 'bank_account.dart';

void main() {
  var account = BankAccount();
  account.deposit(100);
  print(account.balance);    // 100.0
  account.close();           // Balance is 100.0

  // account._balance = 1e9; // ERROR: '_balance' isn't defined (private to bank_account.dart)
}
```

Key points:

- Privacy applies to **any** name: fields, methods, constructors, classes, top-level functions, and variables.
- Because the boundary is the *file*, other classes in the same file *can* access each other's private members.
- This design encourages **encapsulation**: keep fields private and expose only what other code needs (through getters, setters, and methods), so you can change the inside later without breaking users of the class.

Libraries, imports, and `part` files are covered in depth in Lesson 36.

---

[Previous](./[19]-Typedefs.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[21]-Constructors.md)