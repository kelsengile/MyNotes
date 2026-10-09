[Previous](./[27]-Generics.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[29]-Exceptions.md)

*Object-Oriented Dart*

# Lesson 28 - Object Equality, Immutability & Value Types

When are two objects "the same"? Two variables can point to one object, or to two different objects that hold identical data. Dart treats these cases differently, and getting them right matters for `Set`s, `Map` keys, comparisons, testing, and state management. This lesson also shows how to design **immutable value types**: small classes whose objects never change and are compared by their contents.

> Lesson 22 introduced overriding `==` and `hashCode`. This lesson explains the rules behind them and combines them with immutability.

---

## 28.1 Identity vs Equality

Dart has two different questions you can ask about two objects:

| Question | Tool | Meaning |
|---|---|---|
| Are they the **very same object** in memory? | `identical(a, b)` | **Identity** |
| Do they **represent the same value**? | `a == b` | **Equality** |

### The default: `==` is identity

Every class inherits `operator ==` from `Object`, and by default it only checks identity:

```dart
class Point {
  final int x;
  final int y;
  Point(this.x, this.y);
}

void main() {
  var a = Point(1, 2);
  var b = Point(1, 2);
  var c = a;

  print(a == b);            // false: same data, but two different objects
  print(a == c);            // true:  c refers to the very same object
  print(identical(a, c));   // true
  print(identical(a, b));   // false
}
```

### Types that already compare by value

Many built-in types override `==` to compare contents:

```dart
void main() {
  print('dart' == 'da' + 'rt');       // true: strings compare by characters
  print(1 == 1.0);                    // true: numbers compare by numeric value
  print(identical(1, 1.0));           // false: an int and a double are different objects
  print((1, 'a') == (1, 'a'));        // true: records compare field by field (Lesson 16)
}
```

### Collections compare by identity

`List`, `Set`, and `Map` do **not** compare contents with `==`:

```dart
void main() {
  var x = [1, 2];
  var y = [1, 2];

  print(x == y);                              // false
  print(x == x);                              // true
  print(const [1, 2] == const [1, 2]);        // true: identical constants are shared (Lesson 21)
}
```

To compare contents you need a helper. The `collection` package (see Lesson 37 on adding packages) provides them:

```dart
import 'package:collection/collection.dart';

void main() {
  var x = [1, 2];
  var y = [1, 2];

  print(const ListEquality<int>().equals(x, y));                  // true
  print(const DeepCollectionEquality().equals(
      {'a': [1, 2]}, {'a': [1, 2]}));                              // true (nested)
}
```

### Special cases

- `a != b` is simply `!(a == b)`. You never override `!=` separately.
- `null == null` is `true`. Dart handles `null` before calling your `==`, so your operator is never called with a `null` receiver.
- `double.nan == double.nan` is `false`: NaN is not equal to anything, including itself.
- Enum values are compared by identity, which is exactly right because each value exists once (Lesson 18).
- `const` objects with equal arguments are **canonicalized**: they are the *same* object, so `==` and `identical` are both `true` (Lesson 21).

### Which one should I use?

Use `==` almost always. Use `identical` only when you truly care about the exact object, such as checking whether a cache returned the same instance.

---

## 28.2 Overriding `==` and `hashCode`

If your class represents a **value** (a point, a money amount, a date range, a coordinate), two objects with the same data should be equal. Override `==` and `hashCode` together.

```dart
class Money {
  final int cents;
  final String currency;

  const Money(this.cents, this.currency);

  @override
  bool operator ==(Object other) =>
      identical(this, other) ||
      other is Money && other.cents == cents && other.currency == currency;

  @override
  int get hashCode => Object.hash(cents, currency);

  @override
  String toString() => '$currency ${(cents / 100).toStringAsFixed(2)}';
}

void main() {
  var a = Money(500, 'USD');
  var b = Money(500, 'USD');
  var c = Money(999, 'USD');

  print(a == b);   // true
  print(a == c);   // false
  print(a);        // USD 5.00
}
```

How it works:

1. `identical(this, other)` is a fast shortcut for the same object.
2. `other is Money` makes sure the other object is the right kind (it also handles `null`).
3. Each significant field is compared.

### The contract

A correct `==` must obey these rules:

| Rule | Meaning |
|---|---|
| **Reflexive** | `a == a` is always `true` |
| **Symmetric** | `a == b` gives the same answer as `b == a` |
| **Transitive** | if `a == b` and `b == c`, then `a == c` |
| **Consistent** | repeated calls give the same answer while the objects are unchanged |
| **Hash rule** | if `a == b`, then `a.hashCode == b.hashCode` |

The last rule is why you must override **both** together. Equal objects with different hash codes break every hash-based collection.

### Why `hashCode` matters

`Set` and `Map` (the default implementations) use `hashCode` first to find a bucket, and `==` second to confirm a match:

```dart
void main() {
  var prices = {Money(500, 'USD'), Money(500, 'USD'), Money(100, 'USD')};
  print(prices.length);      // 2: duplicates are removed

  var labels = {Money(500, 'USD'): 'five dollars'};
  print(labels[Money(500, 'USD')]);   // five dollars: a different object finds the entry
}
```

If you override `==` but forget `hashCode`, the code may seem to work for `==` alone but `Set` and `Map` will misbehave, because two equal objects land in different buckets. The `hash_and_equals` lint reports this mistake.

### Building the hash code

| Tool | Use |
|---|---|
| `Object.hash(a, b, c)` | Combines up to 20 values into one hash code |
| `Object.hashAll(iterable)` | Combines every element of a collection |
| `Object.hashAllUnordered(iterable)` | Same, but the order of elements does not matter (for sets) |

Use the **same fields** in `==` and `hashCode`.

### A compact idiom with records

Records already compare and hash structurally, so you can borrow that:

```dart
class Temperature {
  final double degrees;
  final String unit;

  const Temperature(this.degrees, this.unit);

  @override
  bool operator ==(Object other) =>
      other is Temperature && (degrees, unit) == (other.degrees, other.unit);

  @override
  int get hashCode => (degrees, unit).hashCode;
}
```

### Fields that are collections

Because lists compare by identity, include them with a collection-aware tool:

```dart
import 'package:collection/collection.dart';

class Playlist {
  final String name;
  final List<String> songs;

  const Playlist(this.name, this.songs);

  @override
  bool operator ==(Object other) =>
      other is Playlist &&
      name == other.name &&
      const ListEquality<String>().equals(songs, other.songs);

  @override
  int get hashCode => Object.hash(name, Object.hashAll(songs));
}
```

### Common pitfalls

- **Mutable fields in `hashCode`.** If an object is stored in a `Set` or used as a `Map` key and then a field used in its hash changes, it is "lost": the collection looks in the wrong bucket. Make value classes immutable (28.3).
- **Subclasses and symmetry.** With `other is Money`, a `SpecialMoney extends Money` could equal a `Money` in one direction but not the other. To require the exact same class, add `other.runtimeType == runtimeType`:

```dart
@override
bool operator ==(Object other) =>
    identical(this, other) ||
    other is Money &&
        other.runtimeType == runtimeType &&
        other.cents == cents &&
        other.currency == currency;
```

- **Comparing different kinds.** Do not make a `Money` equal to an `int`. `==` should only be `true` for objects of compatible types.
- **Do not compare unrelated or derived fields.** Only fields that define the identity of the *value* belong in `==`.
- **Writing it all by hand is repetitive.** Packages such as `equatable` and the code generator `freezed` (Lesson 47) generate these members for you. Understanding the rules above is still important so that you can verify what they do.

---

## 28.3 Designing Immutable Classes

An **immutable** object cannot change after it is created. To "change" it you create a new one. Value types such as numbers, strings, `DateTime`, and records already work this way.

### The recipe

1. Make **every field `final`**.
2. Provide a **`const` constructor** when all fields allow it.
3. Do not expose setters.
4. Make sure fields that hold collections or other objects are immutable too.
5. Provide `==`, `hashCode`, and `toString` so the object behaves as a value.

```dart
class Address {
  final String street;
  final String city;

  const Address(this.street, this.city);

  @override
  bool operator ==(Object other) =>
      other is Address && street == other.street && city == other.city;

  @override
  int get hashCode => Object.hash(street, city);

  @override
  String toString() => '$street, $city';
}

void main() {
  const home = Address('1 Main St', 'Manila');
  // home.city = 'Cebu';    // ERROR: 'city' is final
  print(home);              // 1 Main St, Manila
}
```

### Changing an immutable object: return a new one

Methods that "modify" the object return a **new instance**:

```dart
class Counter {
  final int value;
  const Counter(this.value);

  Counter increment() => Counter(value + 1);
  Counter reset() => const Counter(0);
}

void main() {
  const a = Counter(0);
  var b = a.increment().increment();
  print(a.value);   // 0: unchanged
  print(b.value);   // 2
}
```

### `final` is shallow: watch your collections

A `final` field cannot be re-assigned, but if it holds a mutable list, the **contents** of that list can still change:

```dart
class Playlist {
  final String name;
  final List<String> songs;
  Playlist(this.name, this.songs);
}

void main() {
  var songs = ['A', 'B'];
  var p = Playlist('Mix', songs);

  songs.add('C');           // the caller still holds the list...
  p.songs.add('D');         // ...and so does anyone with access to p
  print(p.songs);           // [A, B, C, D]: the "immutable" object changed!
}
```

Fix it with an **unmodifiable copy**:

```dart
class Playlist {
  final String name;
  final List<String> songs;

  Playlist(this.name, List<String> songs)
      : songs = List.unmodifiable(songs);   // a private, read-only copy
}

void main() {
  var original = ['A', 'B'];
  var p = Playlist('Mix', original);

  original.add('C');
  print(p.songs);          // [A, B]: unaffected by the caller's later change
  // p.songs.add('D');     // throws UnsupportedError: the list is unmodifiable
}
```

Two kinds of read-only lists exist:

| | What it is | Notes |
|---|---|---|
| `List.unmodifiable(source)` | A **copy** that cannot be changed | Safe from later changes to `source` |
| `UnmodifiableListView(source)` (from `dart:collection`) | A read-only **view** of `source` | Reflects later changes made to `source` |
| `const [1, 2, 3]` | A compile-time constant list | Cannot be changed and is shared |

The same ideas apply to `Set.unmodifiable`, `Map.unmodifiable`, and `const` sets and maps (see Lesson 15).

### `final`, `const`, and "immutable"

| Term | What it applies to |
|---|---|
| `final` variable or field | The *reference* cannot be re-assigned |
| `const` value | A compile-time constant: deeply immutable and shared |
| Immutable object | An object whose fields are all `final` and whose fields are themselves immutable |

### The `@immutable` annotation

The `meta` package (re-exported by Flutter) provides `@immutable`. It makes the analyzer warn you if the class, or any subclass, declares a non-`final` field:

```dart
import 'package:meta/meta.dart';

@immutable
class Score {
  final int points;
  const Score(this.points);
  // int bonus = 0;   // analyzer warning: all fields of an @immutable class must be final
}
```

### Why bother with immutability?

- **Predictability:** nobody can change an object behind your back.
- **Safe as keys:** immutable objects keep the same hash code, so they are reliable in `Set` and `Map`.
- **Safe to share:** the same object can be passed around freely, including between isolates (Lesson 35).
- **Easy change detection:** a state-management system can tell "something changed" by seeing a new object instead of inspecting every field. This is why Flutter state is usually immutable.
- **Fewer bugs and simpler testing:** there is no hidden state to set up or reset.
- **Memory sharing:** `const` instances are canonicalized (Lesson 21).

The trade-off is that changing a value allocates a new object. For small value types this cost is negligible; for very large data that changes in a tight loop, a mutable structure may be the better choice. Choose immutability by default and make exceptions deliberately.

---

## 28.4 `copyWith` Pattern

Creating a modified copy by hand is tedious when a class has many fields. The conventional solution is a `copyWith` method that takes optional replacement values and fills in the rest from the current object.

```dart
class User {
  final String name;
  final int age;
  final String? email;

  const User({required this.name, required this.age, this.email});

  User copyWith({String? name, int? age, String? email}) {
    return User(
      name: name ?? this.name,
      age: age ?? this.age,
      email: email ?? this.email,
    );
  }

  @override
  bool operator ==(Object other) =>
      other is User &&
      name == other.name &&
      age == other.age &&
      email == other.email;

  @override
  int get hashCode => Object.hash(name, age, email);

  @override
  String toString() => 'User($name, $age, $email)';
}

void main() {
  const ana = User(name: 'Ana', age: 21);

  var older = ana.copyWith(age: 22);
  var withMail = older.copyWith(email: 'ana@example.com');

  print(ana);        // User(Ana, 21, null)
  print(older);      // User(Ana, 22, null)
  print(withMail);   // User(Ana, 22, ana@example.com)
}
```

How it works: each parameter is nullable and optional. `name ?? this.name` means "use the new value if one was given, otherwise keep the old one." The original object is never touched.

### The limitation: you cannot set a field to `null`

Because `null` means "not provided," this does not work:

```dart
var cleared = withMail.copyWith(email: null);
print(cleared.email);   // ana@example.com: still there!
```

A common fix is to wrap nullable fields in a function, so that "no argument" and "explicit null" are different:

```dart
User copyWith({
  String? name,
  int? age,
  String? Function()? email,        // a function that returns the new email (or null)
}) {
  return User(
    name: name ?? this.name,
    age: age ?? this.age,
    email: email != null ? email() : this.email,
  );
}

void main() {
  const u = User(name: 'Ana', age: 21, email: 'ana@example.com');

  print(u.copyWith(age: 22).email);              // ana@example.com (kept)
  print(u.copyWith(email: () => null).email);    // null (explicitly cleared)
  print(u.copyWith(email: () => 'new@x.com').email);   // new@x.com
}
```

You only need this trick for nullable fields that you really need to clear. Many classes do not.

### Updating nested and collection fields

Update collections by building a new one, never by mutating:

```dart
class Todo {
  final String title;
  final List<String> tags;

  Todo(this.title, List<String> tags) : tags = List.unmodifiable(tags);

  Todo copyWith({String? title, List<String>? tags}) =>
      Todo(title ?? this.title, tags ?? this.tags);

  Todo addTag(String tag) => copyWith(tags: [...tags, tag]);       // spread (Lesson 15)
  Todo removeTag(String tag) =>
      copyWith(tags: tags.where((t) => t != tag).toList());
}

void main() {
  var t = Todo('Write lesson', ['dart']);
  var t2 = t.addTag('docs');

  print(t.tags);    // [dart]
  print(t2.tags);   // [dart, docs]
}
```

For nested immutable objects, call `copyWith` at each level: `state.copyWith(user: state.user.copyWith(age: 30))`.

### Putting it together: a complete value type

A well-designed value class has all of these pieces:

| Piece | Purpose |
|---|---|
| `final` fields and a `const` constructor | Immutability |
| `==` and `hashCode` | Value equality |
| `toString` | Readable output for debugging and logs |
| `copyWith` | Convenient modified copies |

Writing these by hand is a good way to learn, but for large projects, code generation (the `freezed` package, Lesson 47) or the `equatable` package can produce them for you. Dart's **records** (Lesson 16) are a lighter alternative when you only need to group a few values without writing a class.

---

[Previous](./[27]-Generics.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[29]-Exceptions.md)
