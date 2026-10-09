[Previous](./[23]-Inheritance.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[25]-Class-Modifiers.md)

*Object-Oriented Dart*

# Lesson 24 - Mixins

Single inheritance is simple, but sometimes several unrelated classes need the same behavior. A bird and an airplane can both fly, yet neither is a kind of the other. **Mixins** let you write a piece of behavior once and add it to as many classes as you like, without forcing them into the same inheritance chain.

---

## 24.1 What Is a Mixin?

A **mixin** is a collection of methods and fields that can be "mixed into" a class. You declare one with the `mixin` keyword:

```dart
mixin Swimmer {
  void swim() => print('Swimming...');
}

mixin Flyer {
  void fly() => print('Flying...');
}
```

Then add them to a class with `with`:

```dart
class Animal {
  final String name;
  Animal(this.name);
}

class Duck extends Animal with Swimmer, Flyer {
  Duck(super.name);
}

class Fish extends Animal with Swimmer {
  Fish(super.name);
}

void main() {
  var duck = Duck('Donald');
  duck.swim();   // Swimming...
  duck.fly();    // Flying...

  var fish = Fish('Nemo');
  fish.swim();   // Swimming...
  // fish.fly(); // ERROR: Fish has no fly()
}
```

`Duck` gets code from `Animal` (by inheriting) **and** from `Swimmer` and `Flyer` (by mixing in). The mixin's members behave exactly as if you had written them inside `Duck`.

### Mixins with state

Mixins can contain fields, getters, setters, and methods:

```dart
mixin Counter {
  int count = 0;
  void increment() => count++;
}

class Clicker with Counter {}

void main() {
  var c = Clicker();
  c.increment();
  c.increment();
  print(c.count);   // 2
}
```

### Restrictions

- A mixin **cannot have a constructor**.
- A mixin **cannot use `extends`** (but can use `on`, see 24.2).
- A mixin cannot be instantiated on its own: `Swimmer()` is an error.
- A mixin type works as a type: `Swimmer s = duck;` is allowed, and `duck is Swimmer` is `true`.

### Order matters

When a class uses several mixins, they are applied from **left to right**, and if two define the same member, the **last one wins**:

```dart
mixin A { String hello() => 'A'; }
mixin B { String hello() => 'B'; }

class Both with A, B {}   // B comes last

void main() {
  print(Both().hello());   // B
}
```

---

## 24.2 `with` and `on`

### `with`: using a mixin

As shown above, `with` follows the `extends` clause (if any) and precedes `implements`:

```dart
class Child extends Parent with MixinOne, MixinTwo implements SomeInterface {}
```

### `on`: restricting who can use a mixin

Sometimes a mixin only makes sense for a certain kind of class, because it needs to call that class's methods. The **`on`** clause restricts the mixin so that it can only be applied to classes that extend or implement the given type. In return, the mixin may use that type's members and call `super`.

```dart
class Musician {
  void perform() => print('Performing');
}

mixin Singer on Musician {
  void sing() {
    perform();                  // allowed: Singer is "on" Musician
    print('...and singing');
  }
}

class Vocalist extends Musician with Singer {}   // OK: extends Musician

// class Robot with Singer {}    // ERROR: Robot is not a Musician

void main() {
  Vocalist().sing();
  // Performing
  // ...and singing
}
```

### Using `super` inside a mixin

With an `on` clause, a mixin can wrap an existing method:

```dart
class Greeter {
  String greet() => 'Hello';
}

mixin Excited on Greeter {
  @override
  String greet() => '${super.greet()}!!!';
}

class LoudGreeter extends Greeter with Excited {}

void main() {
  print(LoudGreeter().greet());   // Hello!!!
}
```

### Abstract members in mixins

A mixin may declare an abstract member that the using class must supply:

```dart
mixin Describable {
  String get name;                       // the class must provide this

  String describe() => 'This is $name';
}

class Car with Describable {
  @override
  String get name => 'a car';
}

void main() {
  print(Car().describe());   // This is a car
}
```

---

## 24.3 Mixin Classes

Since Dart 3, an ordinary `class` **cannot** be used as a mixin by default. If you want a type that works both as a normal class and as a mixin, declare it with `mixin class`:

```dart
mixin class Musician {
  void play() => print('Playing music');
}

// Used as a normal class
class Band extends Musician {}

// Used as a mixin
class Student with Musician {}

void main() {
  Musician().play();   // Playing music (it can be instantiated)
  Band().play();       // Playing music
  Student().play();    // Playing music
}
```

Restrictions on a `mixin class` are the combined restrictions of both forms:

- It **cannot have a generative constructor with parameters** (a plain constructor with no arguments is fine), since it must be usable as a mixin.
- It **cannot** use `extends`, `with`, or `on`.

Use `mixin class` sparingly. Most of the time you want either a plain class or a plain mixin. Mixin classes are mainly handy for APIs that must support both uses.

You can combine `mixin class` with the other class modifiers from Lesson 25, such as `abstract mixin class` or `base mixin class`.

---

## 24.4 Mixins vs Inheritance vs Interfaces

Dart has three ways to relate and reuse types. Choosing correctly keeps designs flexible.

| | `extends` (inheritance) | `with` (mixin) | `implements` (interface) |
|---|---|---|---|
| Reuses code from the other type | Yes | Yes | **No** |
| Number allowed | One | Many | Many |
| Models | "is-a" (specialization) | "can-do" (shared ability) | "behaves-like" (a contract) |
| Constructors | Used with `super` | None | None |
| State (fields) | Inherited | Included | You re-declare |

### A comparison

```dart
// Inheritance: a Dog IS an Animal
class Animal {
  void eat() => print('Eating');
}
class Dog extends Animal {}

// Mixin: a Dog CAN swim (shared, reusable ability)
mixin Swimmer {
  void swim() => print('Swimming');
}
class Labrador extends Dog with Swimmer {}

// Interface: a RobotDog promises to look like a Dog, writing its own code
class RobotDog implements Dog {
  @override
  void eat() => print('Recharging');
}
```

### Choosing

- **Use inheritance** for a clear "is-a" relationship where a subclass really is a specialized version of the parent, and where one parent is enough.
- **Use a mixin** when unrelated classes need the same behavior (logging, serialization, a counter, `Comparable` helpers), or when a class needs behavior from more than one source.
- **Use an interface** when you want to define a contract and let each class provide its own implementation, for example for testing with fake objects.

### Caution

- Deep or tangled mixin stacks can be hard to follow, because the order of application decides which method wins. Keep mixins small and focused.
- Prefer **composition** (holding another object in a field) when behavior needs to be swapped at run time. For example, give a `Robot` a `Walker` object instead of mixing walking in.

```dart
class Robot {
  final Walker walker;
  Robot(this.walker);
  void go() => walker.walk();
}

abstract class Walker {
  void walk();
}
```

---

[Previous](./[23]-Inheritance.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[25]-Class-Modifiers.md)