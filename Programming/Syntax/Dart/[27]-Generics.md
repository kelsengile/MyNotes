[Previous](./[26]-Extensions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[28]-Equality-and-Immutability.md)

*Object-Oriented Dart*

# Lesson 27 - Generics

**Generics** let you write a class or function once and use it with many types, while the compiler still checks that you use those types correctly. You have already used them: `List<int>`, `Map<String, double>`, and `Future<String>` are all generic types. This lesson shows how they work and how to write your own.

---

## 27.1 Why Generics?

Suppose you need a small container that holds one value. Without generics, you have two poor choices.

**Option 1: one class per type.** This repeats the same code for every type:

```dart
class IntBox {
  int value;
  IntBox(this.value);
}

class StringBox {
  String value;
  StringBox(this.value);
}
// ...and again for double, bool, User, ...
```

**Option 2: use `dynamic` (or `Object?`).** One class, but no safety:

```dart
class DynamicBox {
  dynamic value;
  DynamicBox(this.value);
}

void main() {
  var box = DynamicBox(5);
  String text = box.value;   // compiles! but crashes at run time:
                             // type 'int' is not a subtype of type 'String'
}
```

**Option 3: generics.** One class, and the compiler knows the type:

```dart
class Box<T> {
  T value;
  Box(this.value);
}

void main() {
  var a = Box<int>(5);
  var b = Box('hello');      // the type argument is inferred: Box<String>

  int n = a.value;           // OK
  // String s = a.value;     // ERROR at compile time: int is not a String
  print(b.value.toUpperCase());   // HELLO: the compiler knows value is a String
}
```

### Vocabulary

| Term | Meaning | Example |
|---|---|---|
| **Type parameter** | A placeholder in the declaration | the `T` in `class Box<T>` |
| **Type argument** | The real type supplied when using it | the `int` in `Box<int>` |
| **Generic type** | A class, mixin, typedef, or extension with type parameters | `Box<T>` |
| **Generic method** | A function or method with its own type parameters | `T first<T>(List<T> items)` |

By convention, type parameters have short, upper-case names: `T` (type), `E` (element), `K` and `V` (key and value), `R` (return type), `S` (a second type).

The benefits:

- **Safety:** mistakes are found by the compiler, not by your users.
- **No casts:** you never need `as int` after reading a value.
- **Reuse:** one implementation serves every type.
- **Better tooling:** autocomplete knows the real type.

---

## 27.2 Generic Classes and Functions

### Generic classes

Put the type parameters in angle brackets after the class name and use them anywhere a type is expected:

```dart
class Stack<T> {
  final List<T> _items = [];

  void push(T item) => _items.add(item);

  T pop() {
    if (_items.isEmpty) throw StateError('Stack is empty');
    return _items.removeLast();
  }

  T? get peek => _items.isEmpty ? null : _items.last;

  bool get isEmpty => _items.isEmpty;
  int get length => _items.length;
}

void main() {
  var numbers = Stack<int>();
  numbers.push(1);
  numbers.push(2);
  print(numbers.pop());      // 2
  print(numbers.peek);       // 1

  // numbers.push('three');  // ERROR: String can't be used as int
}
```

A class can have several type parameters:

```dart
class Pair<A, B> {
  final A first;
  final B second;

  const Pair(this.first, this.second);

  Pair<B, A> swap() => Pair(second, first);

  @override
  String toString() => '($first, $second)';
}

void main() {
  var p = Pair(1, 'one');          // inferred: Pair<int, String>
  print(p);                        // (1, one)
  print(p.swap());                 // (one, 1)
}
```

### Generic functions and methods

A function can declare its own type parameters between the name and the parameter list:

```dart
T firstOr<T>(List<T> items, T fallback) =>
    items.isEmpty ? fallback : items.first;

void main() {
  print(firstOr([10, 20], 0));           // 10   (T inferred as int)
  print(firstOr<String>([], 'none'));    // none (T given explicitly)
}
```

Dart usually **infers** the type argument from the arguments you pass. Give it explicitly (`firstOr<String>(...)`) when inference cannot work it out, for example when the list is empty and nothing else determines `T`.

Methods in a normal class can be generic too:

```dart
class Printer {
  void show<T>(T value) => print('Value: $value, type: $T');
}

void main() {
  Printer().show(42);        // Value: 42, type: int
  Printer().show('hi');      // Value: hi, type: String
}
```

A generic method can also use the **class's** type parameters, and add its own:

```dart
class Box<T> {
  final T value;
  Box(this.value);

  // R is the method's own parameter; T belongs to the class
  Box<R> map<R>(R Function(T value) transform) => Box(transform(value));
}

void main() {
  var length = Box('hello').map((s) => s.length);   // Box<int>
  print(length.value);                              // 5
}
```

### Generic interfaces and abstract classes

Generics pair naturally with abstract classes and `implements` (Lesson 23), because they let one contract describe many kinds of data:

```dart
abstract class Repository<T> {
  void save(T item);
  T? find(int id);
  List<T> all();
}

class User {
  final int id;
  final String name;
  User(this.id, this.name);
}

class InMemoryUserRepository implements Repository<User> {
  final Map<int, User> _users = {};

  @override
  void save(User item) => _users[item.id] = item;

  @override
  User? find(int id) => _users[id];

  @override
  List<User> all() => _users.values.toList();
}
```

### Generic constructors and named constructors

Constructors use the class's type parameters; you write the type arguments after the class name:

```dart
class Box<T> {
  final T value;
  Box(this.value);
  Box.fromList(List<T> list) : value = list.first;
}

void main() {
  var a = Box<int>(1);
  var b = Box<String>.fromList(['x', 'y']);
}
```

### Other generic declarations

- **Typedefs:** `typedef StringMap<V> = Map<String, V>;` (Lesson 19).
- **Mixins:** `mixin Cache<K, V> { final Map<K, V> cache = {}; }` (Lesson 24).
- **Extensions:** `extension Chunking<T> on List<T> { ... }` (Lesson 26).
- **Function types:** `typedef Mapper<T, R> = R Function(T input);`

---

## 27.3 Bounded Type Parameters (`extends`)

A plain `T` could be **any** type, so inside the class you can only use what every object has. To use more, restrict the type parameter with `extends`. This is called a **bound**.

```dart
num sumAll<T extends num>(List<T> values) {
  num total = 0;
  for (final v in values) {
    total += v;       // OK: T is guaranteed to be a num
  }
  return total;
}

void main() {
  print(sumAll([1, 2, 3]));        // 6
  print(sumAll([1.5, 2.5]));       // 4.0
  // sumAll(['a', 'b']);           // ERROR: String does not extend num
}
```

### Bounding by your own types

```dart
abstract class Animal {
  String get name;
  void speak();
}

class Dog implements Animal {
  @override
  String get name => 'Rex';
  @override
  void speak() => print('Woof');
}

class Shelter<T extends Animal> {
  final List<T> _animals = [];

  void admit(T animal) => _animals.add(animal);

  void introduceAll() {
    for (final a in _animals) {
      print(a.name);     // OK: every T is an Animal
      a.speak();
    }
  }
}

void main() {
  var dogs = Shelter<Dog>();
  dogs.admit(Dog());
  dogs.introduceAll();
  // var bad = Shelter<String>();   // ERROR: String is not an Animal
}
```

Notice that `Shelter<Dog>` keeps the exact type: `admit` accepts only a `Dog`, and nothing is lost the way it would be if the parameter were just `Animal`.

### Bounding by an interface

A common bound is `Comparable`, which lets you compare values generically:

```dart
T largest<T extends Comparable<Object?>>(List<T> items) {
  var best = items.first;
  for (final item in items.skip(1)) {
    if (item.compareTo(best) > 0) best = item;
  }
  return best;
}

void main() {
  print(largest([3, 9, 2]));              // 9
  print(largest(['pear', 'apple']));      // pear
  print(largest([2.5, 1.5]));             // 2.5
}
```

Here the bound `Comparable<Object?>` is deliberately loose, so it accepts numbers, strings, and your own classes that implement `Comparable`.

### Non-nullable bounds

If you do not write a bound, a type parameter is bounded by `Object?`, which **includes `null`**. Write `extends Object` to forbid nullable type arguments:

```dart
class Required<T extends Object> {
  final T value;
  Required(this.value);
}

void main() {
  var a = Required<int>(1);        // OK
  // var b = Required<int?>(null); // ERROR: int? does not extend Object
}
```

### Rules

- A bound applies everywhere the parameter is used, including in subclasses: `class SpecialShelter<T extends Animal> extends Shelter<T> {}`.
- A type parameter can refer to itself in its bound (as `T extends Comparable<T>` style declarations do). This is useful for types that compare or copy themselves.
- You can only bound by **one** type. To require several capabilities, create a type that combines them (for example an abstract class that implements both interfaces) and bound by that.

---

## 27.4 Generic Collections and Type Safety

Dart's collections are generic, and the type argument is what keeps them safe.

```dart
var ids = <int>[1, 2, 3];
var names = <String>{'Ana', 'Ben'};
var ages = <String, int>{'Ana': 21, 'Ben': 19};
var matrix = <List<int>>[[1, 2], [3, 4]];
Map<String, List<int>> scores = {'Ana': [90, 85]};
```

### Inference from literals

Dart infers the element type from the values you write:

```dart
var a = [1, 2, 3];        // List<int>
var b = [1, 2.5];         // List<num>
var c = [1, 'two'];       // List<Object>
var d = [];               // List<dynamic>  (nothing to infer from!)
var e = <int>[];          // List<int>      (empty list, so be explicit)
var f = {};               // Map<dynamic, dynamic>  (curly braces default to a map)
var g = <int>{};          // Set<int>
```

An empty literal has nothing to infer from, so give it a type with `<Type>[]`, or declare the variable type: `List<int> list = [];`. Avoid `List<dynamic>` where you can, because it throws away the safety generics provide.

### Writing generic helpers over collections

```dart
Map<K, List<V>> groupBy<K, V>(Iterable<V> items, K Function(V item) keyOf) {
  final result = <K, List<V>>{};
  for (final item in items) {
    result.putIfAbsent(keyOf(item), () => []).add(item);
  }
  return result;
}

void main() {
  var words = ['apple', 'avocado', 'banana', 'blueberry', 'cherry'];
  print(groupBy(words, (w) => w[0]));
  // {a: [apple, avocado], b: [banana, blueberry], c: [cherry]}
}
```

Dart works out `K = String` and `V = String` on its own from the arguments.

### Collections are covariant (a surprising trap)

In Dart, if `int` is a subtype of `num`, then `List<int>` is a subtype of `List<num>`. This is called **covariance**, and it is convenient for reading but allows a mistake the compiler cannot catch:

```dart
void main() {
  List<int> ints = [1, 2, 3];
  List<num> nums = ints;       // allowed: List<int> is a List<num>

  print(nums.first);           // 1   (reading is fine)
  nums.add(1.5);               // RUNTIME TypeError! The real list only holds ints.
}
```

Writing to the list through the wider type is unsafe, so Dart checks it at run time and throws. How to avoid it:

- Expose read-only views as `Iterable<num>` instead of `List<num>` when callers should not add items.
- Make a real copy if you need a list that accepts more kinds: `List<num> nums = List<num>.from(ints);`.

### Converting and filtering by type

```dart
void main() {
  List<Object> mixed = [1, 'two', 3, 'four'];

  var onlyInts = mixed.whereType<int>().toList();   // [1, 3]  (safe: skips others)
  var strings = mixed.whereType<String>().toList(); // [two, four]

  // cast<T>() reinterprets every element and throws if one does not fit
  var risky = mixed.cast<int>();    // lazy; fails when you read a String
}
```

Prefer `whereType<T>()` to select by type. Use `cast<T>()` only when you are certain every element has that type.

### Data from outside (JSON)

Parsed JSON arrives as `dynamic` or `List<dynamic>`. Convert it into a typed collection at the boundary of your program, once, instead of carrying `dynamic` around:

```dart
List<dynamic> raw = [1, 2, 3];               // pretend this came from jsonDecode
List<int> typed = List<int>.from(raw);       // checks and copies
print(typed.map((n) => n * 2).toList());     // [2, 4, 6]
```

---

## 27.5 Reified Generics and Runtime Types

In some languages (Java, for example) generic type arguments are **erased** after compilation: at run time a `List<String>` is just a `List`. Dart is different: its generics are **reified**, meaning the type arguments exist at run time and can be inspected.

```dart
void main() {
  var list = <int>[1, 2, 3];

  print(list is List<int>);         // true
  print(list is List<String>);      // false
  print(list.runtimeType);          // List<int>
}
```

### Using type parameters at run time

Because `T` is a real value at run time, you can use it in `is` and `as` checks, create typed collections with it, and print it:

```dart
bool isOfType<T>(Object? value) => value is T;

List<T> onlyOfType<T>(List<Object?> items) => items.whereType<T>().toList();

class Container<T> {
  final List<T> items = <T>[];     // a list of exactly T

  void describe() => print('Container of $T');   // T prints as a type name
}

void main() {
  print(isOfType<String>('hi'));                     // true
  print(isOfType<int>('hi'));                        // false
  print(onlyOfType<int>([1, 'a', 2, null]));         // [1, 2]
  Container<double>().describe();                    // Container of double
}
```

You can also pass types around as values of type `Type`:

```dart
Type typeOf<T>() => T;

void main() {
  print(typeOf<List<String>>());        // List<String>
  print(typeOf<int>() == int);          // true
}
```

### Reification explains the run-time error from 27.4

The list in the previous section knew it was a `List<int>`, so adding a `double` failed when the program ran:

```dart
void main() {
  List<Object> objects = <int>[1, 2];   // the real object is a List<int>
  objects.add('three');                 // TypeError: 'String' is not a subtype of 'int'
}
```

In an erased language this mistake would silently corrupt the list. In Dart it fails right away.

### A type parameter is not a constructor

A type parameter is a type, not a class you can instantiate or call static members on:

```dart
T create<T>() {
  // return T();            // ERROR: T is not a constructor
  // return T.parse('1');   // ERROR: static members are not accessible through T
  throw UnimplementedError();
}
```

If you need to build instances, pass a factory function: `T create<T>(T Function() make) => make();`.

A common workaround is to compare types explicitly:

```dart
T defaultFor<T>() {
  if (T == int) return 0 as T;
  if (T == String) return '' as T;
  throw ArgumentError('No default value for $T');
}

void main() {
  print(defaultFor<int>());          // 0
  print(defaultFor<String>());       // (empty string)
}
```

Keep in mind that `T == int` is false for `int?`, because `int` and `int?` are different types.

### Summary of cautions

- `runtimeType` and `Type` names are meant for debugging. Do not build logic that depends on their exact text, because compiled-to-JavaScript builds can change the printed names (for example when code is minified).
- Prefer `is T` checks and normal polymorphism to string comparisons of type names.
- Generics are checked at compile time first and at run time second. Aim to satisfy the compile-time checks so the run-time checks never fire.

---

[Previous](./[26]-Extensions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[28]-Equality-and-Immutability.md)
