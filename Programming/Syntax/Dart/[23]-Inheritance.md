
[Previous](./[22]-Getters-Setters-Operators.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[24]-Mixins.md)

*Object-Oriented Dart*

# Lesson 23 - Inheritance & Polymorphism

**Inheritance** lets one class build on another, reusing and specializing its behavior. **Polymorphism** ("many forms") means code written for a general type can work with many specific types. Together they are the backbone of object-oriented design.

---

## 23.1 `extends` and `super`

Use `extends` to create a **subclass** (child) that inherits the fields and methods of a **superclass** (parent):

```dart
class Animal {
  final String name;

  Animal(this.name);

  void eat() => print('$name is eating.');
  void sleep() => print('$name is sleeping.');
}

class Dog extends Animal {
  Dog(super.name);

  void bark() => print('$name says Woof!');
}

void main() {
  var dog = Dog('Rex');
  dog.eat();     // Rex is eating.   (inherited)
  dog.bark();    // Rex says Woof!   (its own)
}
```

`Dog` automatically has `name`, `eat()`, and `sleep()`. This models an **"is-a"** relationship: a `Dog` *is an* `Animal`.

Facts:

- Dart supports **single inheritance**: a class can extend only **one** class. (Mixins and interfaces, in the next lessons, provide more reuse.)
- Every class ultimately extends `Object`.
- Constructors are **not inherited**. The subclass must declare its own and pass the needed values to the superclass constructor.

### Calling the superclass constructor

```dart
class Vehicle {
  final String brand;
  Vehicle(this.brand);
}

class Car extends Vehicle {
  final int doors;

  // Option 1: super parameters (preferred)
  Car(super.brand, this.doors);

  // Option 2: explicit call in the initializer list
  Car.sedan(String brand) : doors = 4, super(brand);
}
```

The superclass constructor runs **before** the subclass constructor body.

### `super` for methods

`super` also refers to the parent's version of a member, which is useful when you extend behavior rather than replace it:

```dart
class Logger {
  void log(String message) => print('LOG: $message');
}

class TimedLogger extends Logger {
  @override
  void log(String message) {
    super.log('[12:00] $message');   // reuse the parent's behavior
  }
}

void main() {
  TimedLogger().log('Started');   // LOG: [12:00] Started
}
```

---

## 23.2 Overriding Methods (`@override`)

A subclass can **override** an inherited method by declaring one with the same name and compatible signature. Mark it with the `@override` annotation:

```dart
class Animal {
  void speak() => print('Some sound');
}

class Dog extends Animal {
  @override
  void speak() => print('Woof!');
}

class Cat extends Animal {
  @override
  void speak() => print('Meow!');
}

void main() {
  Animal().speak();   // Some sound
  Dog().speak();      // Woof!
  Cat().speak();      // Meow!
}
```

`@override` is optional for the compiler but **strongly recommended**: if you misspell the name or the parent changes, the analyzer warns you instead of silently creating a new, unrelated method.

You can override getters, setters, and operators too (including `toString`, `==`, and `hashCode`).

### Polymorphism in action

A variable of a parent type can hold any subtype, and Dart calls the **actual object's** version of the method at run time:

```dart
void main() {
  List<Animal> animals = [Dog(), Cat(), Animal()];

  for (final a in animals) {
    a.speak();   // Woof!  Meow!  Some sound
  }
}
```

The loop does not need to know which kind of animal each item is. Adding a new `Bird` subclass later requires **no change** to this loop.

### Override rules

An overriding method must be a valid replacement for the original, so callers cannot tell the difference:

- It must accept **at least** the same parameters (it may add optional ones).
- Its return type must be the same as, or a **subtype** of, the original's.
- A parameter type may be broader, but not narrower (unless marked `covariant`, an advanced feature).

```dart
class Shape {
  Object describe() => 'shape';
}

class Circle extends Shape {
  @override
  String describe() => 'circle';   // OK: String is a subtype of Object
}
```

### Type tests

Use `is` to check the actual type, and Dart promotes the variable:

```dart
void inspect(Animal a) {
  if (a is Dog) {
    print('It is a dog');   // a is promoted to Dog inside this block
  }
}
```

---

## 23.3 Abstract Classes and Methods

An **abstract class** is a class that cannot be instantiated directly. It exists to be extended. It may declare **abstract methods**: methods with a signature but **no body**, which every concrete subclass must implement.

```dart
abstract class Shape {
  double area();                    // abstract: no body
  double perimeter();               // abstract

  void describe() {                 // concrete: shared by all shapes
    print('Area: ${area().toStringAsFixed(2)}, '
        'Perimeter: ${perimeter().toStringAsFixed(2)}');
  }
}

class Circle extends Shape {
  final double radius;
  Circle(this.radius);

  @override
  double area() => 3.14159 * radius * radius;

  @override
  double perimeter() => 2 * 3.14159 * radius;
}

class Rectangle extends Shape {
  final double w, h;
  Rectangle(this.w, this.h);

  @override
  double area() => w * h;

  @override
  double perimeter() => 2 * (w + h);
}

void main() {
  // var s = Shape();   // ERROR: abstract classes can't be instantiated

  List<Shape> shapes = [Circle(1), Rectangle(2, 3)];
  for (final s in shapes) {
    s.describe();
  }
  // Area: 3.14, Perimeter: 6.28
  // Area: 6.00, Perimeter: 10.00
}
```

Notes:

- Abstract classes can have fields, constructors, and ordinary methods in addition to abstract ones. They are a mix of a contract (what must exist) and shared code.
- Abstract getters and setters are allowed: `double get area;`.
- A concrete (non-abstract) subclass that forgets to implement an abstract member produces a compile-time error.
- Use an abstract class when subclasses share both **a common interface and some implementation**. If you only need a contract with no shared code, an interface (next section) works.

---

## 23.4 Implicit Interfaces and `implements`

In Dart, **every class implicitly defines an interface** containing all its instance members. Another class can promise to provide those same members with the `implements` keyword:

```dart
class Printer {
  void printText(String text) => print(text);
}

class FancyPrinter implements Printer {
  @override
  void printText(String text) => print('*** $text ***');
}

void main() {
  Printer p = FancyPrinter();
  p.printText('Hello');   // *** Hello ***
}
```

The key difference between `extends` and `implements`:

| | `extends` | `implements` |
|---|---|---|
| Inherits the parent's code | **Yes** | **No** (you rewrite every member) |
| How many can you use | One | **Many** |
| Fields | Inherited | You must provide getters (and setters) for them |
| Constructors / `super` calls | Yes | No |

### Implementing several interfaces

```dart
abstract class Flyer {
  void fly();
}

abstract class Swimmer {
  void swim();
}

class Duck implements Flyer, Swimmer {
  @override
  void fly() => print('Duck flies');

  @override
  void swim() => print('Duck swims');
}
```

A class can both extend one class and implement others:

```dart
class Animal {}

class Duck extends Animal implements Flyer, Swimmer {
  @override
  void fly() => print('Flap');
  @override
  void swim() => print('Paddle');
}
```

### Interfaces of built-in types

You can implement library classes too, for example to make a custom `Comparable`:

```dart
class Version implements Comparable<Version> {
  final int major, minor;
  Version(this.major, this.minor);

  @override
  int compareTo(Version other) {
    if (major != other.major) return major.compareTo(other.major);
    return minor.compareTo(other.minor);
  }
}

void main() {
  var list = [Version(2, 1), Version(1, 5), Version(2, 0)];
  list.sort();   // works because Version is Comparable
  print(list.map((v) => '${v.major}.${v.minor}').join(', '));   // 1.5, 2.0, 2.1
}
```

When using `implements` with a class that has fields, remember that the fields become **getters/setters you must define**. If you just want to reuse code, use `extends` or a mixin instead.

---

## 23.5 `noSuchMethod`

When you call a method that an object does not have, Dart normally reports an error at compile time. Under certain conditions, though, Dart instead calls the object's **`noSuchMethod`** method, which every object inherits from `Object`. By default it throws a `NoSuchMethodError`, but you can override it to handle unknown calls yourself.

```dart
class Ghost {
  @override
  dynamic noSuchMethod(Invocation invocation) {
    print('Called ${invocation.memberName}');
    return null;
  }
}

void main() {
  dynamic ghost = Ghost();     // dynamic: calls are not checked at compile time
  ghost.hello();               // Called Symbol("hello")
  ghost.anything(1, 2, 3);     // Called Symbol("anything")
}
```

The `Invocation` object describes the call: `memberName`, `positionalArguments`, `namedArguments`, and whether it was a getter, setter, or method.

### Implementing an interface without writing every member

A class that declares `implements SomeType` and overrides `noSuchMethod` does not have to write out each member of the interface; unimplemented ones are forwarded to `noSuchMethod`. This is how mock objects (such as those in testing libraries) are built:

```dart
abstract class Service {
  String fetch(String id);
  void save(String id);
}

class FakeService implements Service {
  @override
  dynamic noSuchMethod(Invocation invocation) {
    print('FakeService got: ${invocation.memberName}');
    return super.noSuchMethod(invocation);   // default behavior: throw
  }
}
```

Be aware:

- Static type checking is limited when you use `dynamic`, so typing mistakes go unnoticed until run time.
- It is an advanced feature that most application code never needs. Prefer ordinary interfaces and abstract classes unless you are building something like a proxy, a mock, or a dynamic wrapper.
- The `Symbol("name")` form printed above may differ when code is minified for the web.

---

[Previous](./[22]-Getters-Setters-Operators.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[24]-Mixins.md)