[Previous](./[25]-Class-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[27]-Generics.md)

*Object-Oriented Dart*

# Lesson 26 - Extension Methods & Extension Types

Sometimes you want a type to have a method it does not have: a `capitalize()` on `String`, a `sum` on a list of numbers, or a `UserId` that is not just any `int`. You cannot edit Dart's built-in classes, and subclassing is often impossible (many core types are `final`). Dart solves this with two features: **extensions** add members to an existing type, and **extension types** wrap an existing type in a new, distinct one at no run-time cost.

> Extension methods require Dart 2.7 or later. Extension types require Dart 3.3 or later.

---

## 26.1 Adding Methods to Existing Types

An **extension** adds new members to a type without modifying it, subclassing it, or wrapping it. The syntax is:

```
extension Name on Type {
  // members
}
```

```dart
extension StringExtras on String {
  String get capitalized =>
      isEmpty ? this : '${this[0].toUpperCase()}${substring(1)}';

  bool get isPalindrome {
    final clean = toLowerCase().replaceAll(RegExp(r'[^a-z0-9]'), '');
    return clean == clean.split('').reversed.join();
  }

  String repeat(int times) => List.filled(times, this).join();
}

void main() {
  print('dart'.capitalized);        // Dart
  print('Racecar'.isPalindrome);     // true
  print('ha'.repeat(3));             // hahaha
}
```

Inside the extension:

- `this` is the object the method was called on (the **receiver**).
- You can use the receiver's own members without writing `this`: `isEmpty`, `substring(1)`, and `toLowerCase()` above all belong to the `String` being extended.

### What an extension can contain

| Allowed | Not allowed |
|---|---|
| Methods | Instance fields (no stored state) |
| Getters and setters | Constructors |
| Operators | Overriding existing members (see 26.4) |
| `static` members and `static const` fields | |

```dart
extension DurationShortcuts on int {
  Duration get seconds => Duration(seconds: this);
  Duration get minutes => Duration(minutes: this);
}

extension StringMinus on String {
  String operator -(String other) => replaceAll(other, '');   // operators work too
}

void main() {
  print(5.minutes);          // 0:05:00.000000
  print(30.seconds);         // 0:00:30.000000
  print('banana' - 'a');     // bnn
}
```

Extensions are not limited to your own types. You can add members to `int`, `List`, `Map`, `DateTime`, a Flutter `BuildContext`, or any class from a package.

### Unnamed extensions

If you give an extension no name, it is visible only inside the library (usually the file) that declares it:

```dart
extension on String {
  String get shout => '${toUpperCase()}!';
}
```

Use named extensions for anything you want to share, because a name is needed to import, hide, or explicitly apply them.

### Where extensions apply

An extension is only active where its library is **imported**. Put extensions in their own file and import them like any other code:

```dart
// file: string_extras.dart
extension StringExtras on String {
  String get capitalized =>
      isEmpty ? this : '${this[0].toUpperCase()}${substring(1)}';
}
```

```dart
// file: main.dart
import 'string_extras.dart';
// import 'string_extras.dart' show StringExtras;   // import only this extension

void main() {
  print('hello'.capitalized);   // Hello
}
```

A name that starts with an underscore (`_StringExtras`) is private to its library and cannot be imported elsewhere.

### Applying an extension explicitly

You can call an extension like a wrapper. This is how you resolve a conflict between two extensions that define the same member name:

```dart
void main() {
  print(StringExtras('hello').capitalized);   // Hello
}
```

---

## 26.2 Extensions on Generic and Nullable Types

### Generic extensions

An extension can have **type parameters**, so it can extend a family of types. Lesson 27 covers generics in detail; for now, read `<T>` as "for any element type":

```dart
extension Chunking<T> on List<T> {
  List<List<T>> chunked(int size) {
    final result = <List<T>>[];
    for (var i = 0; i < length; i += size) {
      result.add(sublist(i, i + size > length ? length : i + size));
    }
    return result;
  }
}

extension IterableExtras<T> on Iterable<T> {
  Iterable<T> whereNot(bool Function(T item) test) => where((e) => !test(e));
}

void main() {
  print([1, 2, 3, 4, 5].chunked(2));                    // [[1, 2], [3, 4], [5]]
  print(['a', 'bb', 'ccc'].whereNot((s) => s.length > 1));   // (a)
}
```

### Extensions on a specific type argument

You can target one exact instantiation, so the members exist only for lists of that kind:

```dart
extension Initials on List<String> {
  String get initials => map((word) => word[0].toUpperCase()).join();
}

void main() {
  print(['ada', 'lovelace'].initials);   // AL
  // print([1, 2].initials);             // ERROR: List<int> is not List<String>
}
```

### Bounded type parameters

Combine a generic extension with a bound (`extends`) to make members available only when the elements satisfy a condition:

```dart
extension NumberIterable<T extends num> on Iterable<T> {
  num get total => fold<num>(0, (sum, n) => sum + n);
  double get average => isEmpty ? 0 : total / length;
}

void main() {
  print([1, 2, 3].total);          // 6
  print([1, 2, 3, 4].average);     // 2.5
}
```

Other examples that show up in real code:

```dart
extension MapInversion<K, V> on Map<K, V> {
  Map<V, K> get inverted => {for (final e in entries) e.value: e.key};
}

void main() {
  print({'a': 1, 'b': 2}.inverted);   // {1: a, 2: b}
}
```

### Extensions on nullable types

An extension can be declared on a **nullable** type such as `String?`. This is allowed because extension members are resolved by the *static type*, not by looking inside the object, so they can be safely called even when the value is `null`:

```dart
extension NullableString on String? {
  bool get isNullOrEmpty {
    final self = this;          // copy to a local so Dart can promote it
    return self == null || self.isEmpty;
  }

  String orDefault(String fallback) => isNullOrEmpty ? fallback : this!;
}

void main() {
  String? name;
  print(name.isNullOrEmpty);          // true  (no error, even though name is null)
  print(name.orDefault('Guest'));     // Guest

  String? other = 'Ana';
  print(other.isNullOrEmpty);         // false
}
```

Inside the extension, `this` has the nullable type, so you must check for `null` before using the string's own members. Without the `?` in `on String?`, calling the extension on a nullable variable would be a compile-time error.

---

## 26.3 Extension Types

An **extension type** creates a **new static type** that wraps an existing value, called the **representation type**. At run time the wrapper disappears: the value is just the original object, so there is no extra allocation. You get a distinct type for the compiler and a custom interface for you, for free.

```dart
extension type UserId(int value) {}
extension type OrderId(int value) {}

void loadUser(UserId id) => print('Loading user ${id.value}');

void main() {
  var user = UserId(42);
  var order = OrderId(42);

  loadUser(user);         // OK
  // loadUser(order);     // ERROR: OrderId is not a UserId
  // loadUser(42);        // ERROR: an int is not a UserId
}
```

This fixes the weakness of typedefs from Lesson 19. A typedef `UserId = int` is only a nickname, so the compiler cannot tell the two IDs apart. An extension type makes them different types.

### Anatomy

```dart
extension type const Meters(double value) {
  //                         ^ the representation: a field named `value` of type double
}
```

- `Meters(double value)` is the **primary constructor**. It declares the representation type (`double`) and an instance variable with the given name (`value`).
- `const` is optional and makes a const constructor available.
- The representation field is accessible as `meters.value`. If you want to hide it, give it a private name using a private primary constructor: `extension type Age._(int _value)`.

### Adding members

Extension types can declare methods, getters, operators, and additional constructors, but no other instance fields:

```dart
extension type Meters(double value) {
  Meters.fromKilometers(double km) : this(km * 1000);

  Meters operator +(Meters other) => Meters(value + other.value);

  bool get isLong => value > 1000;

  String toDisplay() => '${value.toStringAsFixed(1)} m';
}

void main() {
  var total = Meters(250) + Meters(800);
  print(total.toDisplay());                   // 1050.0 m
  print(total.isLong);                        // true
  print(Meters.fromKilometers(2).toDisplay());   // 2000.0 m
  // var wrong = total + 5;                   // ERROR: only Meters can be added
}
```

Because `Meters` does not expose `double`'s operators, it only allows what you decided to allow. You cannot accidentally add meters to seconds.

### Validating in a constructor

Extra constructors may run code, so you can reject bad values at the moment you create them:

```dart
extension type Email._(String value) {
  Email(String input) : value = input.trim().toLowerCase() {
    if (!value.contains('@')) {
      throw FormatException('Not an email address', input);
    }
  }
}

void main() {
  var e = Email('  Ana@Example.com ');
  print(e.value);          // ana@example.com
  // Email('nope');        // throws FormatException
}
```

Once you hold an `Email`, you know it was validated. This idea is revisited in Lesson 30.

### Wrapping data with a friendlier API

A very practical use is putting a typed face on loosely typed data such as parsed JSON:

```dart
extension type Person(Map<String, Object?> json) {
  String get name => json['name'] as String;
  int get age => json['age'] as int;
}

void main() {
  var p = Person({'name': 'Ana', 'age': 21});
  print('${p.name} is ${p.age}');   // Ana is 21
}
```

No `Person` object is allocated; `p` is still the same `Map` at run time.

### `implements`: reusing the representation's members

By default an extension type is **not** a subtype of its representation, so none of the wrapped type's members are available. Use `implements` to opt in:

```dart
extension type Count(int value) implements int {
  Count increment() => Count(value + 1);
}

void main() {
  var c = Count(3);
  print(c + 1);              // 4   (int's operators are available)
  int plain = c;             // OK: a Count is an int
  print(c.increment().value);   // 4
}
```

An extension type can also implement another extension type, as long as the representation types are compatible.

### Extension methods vs extension types vs alternatives

| | Extension | Extension type | Wrapper class | Typedef |
|---|---|---|---|---|
| Creates a new type | No | **Yes** | **Yes** | No |
| Run-time cost | None | **None** (erased) | An extra object | None |
| Can hide the wrapped type's members | No | **Yes** | Yes | No |
| Can add stored fields | No | No | Yes | No |
| Best for | Adding helpers to a type | Type-safe IDs, units, validated values, typed views over data | Data that needs its own state and identity | Shortening a long type name |

---

## 26.4 Static Extension Pitfalls

Extensions are convenient but they are resolved **at compile time**, using the *static* type of the expression. Most surprises come from forgetting this.

### 1. Extensions are not applied to `dynamic`

```dart
extension on String {
  String get shout => '${toUpperCase()}!';
}

void main() {
  String s = 'hi';
  print(s.shout);          // HI!

  dynamic d = 'hi';
  // print(d.shout);       // RUNTIME ERROR: NoSuchMethodError
}
```

A `dynamic` call is looked up on the real object at run time, and the real `String` has no `shout` member. The extension only exists in the compiler's view.

### 2. Real members always win

If the type already has a member with that name, the extension's version is **never** chosen. Extensions cannot override or replace anything:

```dart
extension on String {
  int get length => 99;    // useless: String.length always wins
}

void main() {
  print('abc'.length);     // 3
}
```

### 3. Extensions are not polymorphic

Because the choice is made from the static type, extension members are **not virtual**:

```dart
class Animal {}
class Dog extends Animal {}

extension AnimalSpeak on Animal {
  String speak() => 'generic sound';
}

extension DogSpeak on Dog {
  String speak() => 'woof';
}

void main() {
  Animal a = Dog();
  print(a.speak());        // generic sound  (static type is Animal)
  print(Dog().speak());    // woof           (static type is Dog; the more specific extension wins)
}
```

If you need behavior that depends on the real object, use a normal method and overriding (Lesson 23).

### 4. Name conflicts between extensions

If two imported extensions apply to the same type and define the same member name, using that member is an **ambiguity error**. Fix it by hiding one, using an import prefix, or applying one explicitly:

```dart
import 'a.dart';
import 'b.dart' hide StringExtras;          // option 1: hide the conflicting extension

// import 'b.dart' as b;                     // option 2: prefix it
// print(b.StringExtras('x').shout);

// print(StringExtras('x').shout);           // option 3: apply explicitly (extension name from a.dart)
```

If the extensions are on types where one is more specific (for example `Dog` vs `Animal`), the more specific one wins automatically as shown above.

### 5. They are invisible until imported

If code compiles in one file but not another, the cause is often a missing `import` of the file that declares the extension. Editors help by suggesting the import.

### 6. No state

An extension cannot add fields. If you need to attach data to an object you do not own, keep it in a separate structure such as a `Map` or an `Expando`.

### 7. Do not pollute common types

An extension on `Object`, `Object?`, or `dynamic` shows up in autocomplete for **everything** and can collide with other code. Prefer the narrowest type that makes sense, and prefer a plain function when the logic is not really "about" the receiver.

### Extension type pitfalls

- **Runtime checks see only the representation.** The extension type is erased, so `is` and `as` tests check the underlying type:

```dart
extension type UserId(int value) {}
extension type OrderId(int value) {}

void main() {
  Object o = UserId(1);
  print(o is int);        // true
  print(o is OrderId);    // true!  (any int passes this check)
  print(o.runtimeType);   // int
}
```

  Once a value is stored in an `Object` or `dynamic` variable, the compile-time distinction is gone. Keep extension-typed values in their own types.

- **Not a subtype by default.** A `UserId` is not an `int` unless you write `implements int`. Pass `id.value` when an `int` is required.
- **No inherited members.** Unlike a subclass, an extension type exposes only the members you declare (plus those of anything it `implements`).

### Choosing the right tool

| You want to... | Use |
|---|---|
| Add a helper method or getter to an existing type | Extension |
| Make two values of the same underlying type incompatible | Extension type |
| Add stored state or polymorphic behavior | A class (Lessons 20 to 25) |
| Only shorten a long type name | Typedef (Lesson 19) |

---

[Previous](./[25]-Class-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[27]-Generics.md)
