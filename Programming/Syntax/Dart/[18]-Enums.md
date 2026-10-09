[Previous](./[17]-Patterns-and-Destructuring.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[19]-Typedefs.md)

*Core Syntax*

# Lesson 18 - Enums

An **enum** (enumeration) is a type with a small, fixed set of named values, such as days of the week, traffic-light colors, or order statuses. Using an enum instead of loose strings or numbers prevents typos and lets the compiler check that you handled every case.

---

## 18.1 Simple Enums

Declare an enum with the `enum` keyword and list its values:

```dart
enum Color { red, green, blue }

void main() {
  Color favorite = Color.green;
  print(favorite);        // Color.green

  if (favorite == Color.green) {
    print('Nice choice!');
  }
}
```

Why not just use strings like `'green'`?

```dart
String status = 'actve';   // typo: Dart cannot catch this
// Color c = Color.activ;  // ERROR: caught immediately by the analyzer
```

Facts about enums:

- Each value is a **constant** object, created once. Compare with `==` (identity).
- Enum values are named in `lowerCamelCase` by convention; the type name is `UpperCamelCase`.
- An enum **cannot be instantiated** with `Color()`, extended, or have new values added at run time.
- Enums are great as `Map` keys and in `Set`s:

```dart
void main() {
  var counts = <Color, int>{Color.red: 3, Color.blue: 1};
  print(counts[Color.red]);   // 3
}
```

---

## 18.2 Enhanced Enums with Fields and Methods

An enum can behave like a small class: it can have **fields**, **constructors**, **methods**, **getters**, and **static members**. The rules:

- Constructors must be `const`.
- Instance fields must be `final`.
- After the list of values, put a **semicolon** before any members.

```dart
enum Weekday {
  monday('Mon'),
  tuesday('Tue'),
  wednesday('Wed'),
  thursday('Thu'),
  friday('Fri'),
  saturday('Sat'),
  sunday('Sun');   // semicolon ends the list of values

  const Weekday(this.short);

  final String short;

  bool get isWeekend => this == saturday || this == sunday;

  Weekday get next => values[(index + 1) % values.length];
}

void main() {
  var day = Weekday.friday;

  print(day.short);        // Fri
  print(day.isWeekend);    // false
  print(day.next);         // Weekday.saturday
  print(Weekday.sunday.next);   // Weekday.monday
}
```

Each value calls the constructor with its own arguments: `monday('Mon')` is like `const Weekday('Mon')`.

### Multiple fields

```dart
enum Planet {
  mercury(mass: 3.303e+23, radius: 2.4397e6),
  earth(mass: 5.976e+24, radius: 6.37814e6);

  const Planet({required this.mass, required this.radius});

  final double mass;     // in kilograms
  final double radius;   // in meters

  static const double _g = 6.67300E-11;

  double get surfaceGravity => _g * mass / (radius * radius);
}

void main() {
  print(Planet.earth.surfaceGravity.toStringAsFixed(2));   // 9.80
}
```

### Overriding `toString`

By default an enum prints as `TypeName.valueName`. You can customize it:

```dart
enum Level {
  low,
  high;

  @override
  String toString() => 'Level<$name>';
}
```

### Implementing interfaces and using mixins

Enhanced enums can use `implements` and `with` (see Lessons 23 and 24):

```dart
abstract interface class Describable {
  String describe();
}

enum Fruit implements Describable {
  apple,
  banana;

  @override
  String describe() => 'A tasty $name';
}
```

Enums can also be generic (`enum Box<T> { ... }`), though this is uncommon.

---

## 18.3 Enums with `switch`

`switch` and enums are a perfect pair, because the compiler checks that **every value is handled**.

### Switch expression

```dart
enum Status { pending, shipped, delivered, cancelled }

String message(Status s) => switch (s) {
      Status.pending => 'Waiting to be packed',
      Status.shipped => 'On its way',
      Status.delivered => 'Arrived',
      Status.cancelled => 'Order cancelled',
    };

void main() {
  print(message(Status.shipped));   // On its way
}
```

### Switch statement

```dart
void react(Status s) {
  switch (s) {
    case Status.pending:
      print('Packing...');
    case Status.shipped || Status.delivered:
      print('Track the parcel');
    case Status.cancelled:
      print('Refund issued');
  }
}
```

### Why this is safe

Suppose you add a new value, `returned`, to `Status`. Every `switch` above immediately shows an error, because it no longer covers all cases. This is much safer than a chain of `if` statements, where a forgotten case silently does nothing.

If you add a `default` or `_` case, you give up this protection, so use it only when "everything else" really means the same thing.

### Using enum fields instead of a switch

When each value has a fixed piece of data, it can be cleaner to store it in the enum itself (an enhanced enum) than to write a switch elsewhere:

```dart
enum Size {
  small(2.50),
  large(4.25);

  const Size(this.price);
  final double price;
}

void main() {
  print(Size.large.price);   // 4.25
}
```

---

## 18.4 Useful Enum Properties (`name`, `index`, `values`)

Every enum automatically provides these:

### `name`

The value's identifier as a `String`:

```dart
print(Color.green.name);   // green
```

Prefer `.name` over `.toString()` when you need the plain text; `toString()` includes the type name (`Color.green`).

### `index`

The zero-based position of the value in its declaration:

```dart
print(Color.red.index);    // 0
print(Color.blue.index);   // 2
```

Avoid saving `index` to files or databases. If someone reorders or inserts values later, the saved numbers will point at the wrong value. Saving `name` is safer.

### `values`

A list of all the enum's values, in declaration order:

```dart
void main() {
  print(Color.values);          // [Color.red, Color.green, Color.blue]
  print(Color.values.length);   // 3

  for (final c in Color.values) {
    print('${c.index}: ${c.name}');
  }
}
```

### Finding a value by name

The `byName` method on `values` looks up a value from its text. It throws an `ArgumentError` if there is no match:

```dart
void main() {
  var c = Color.values.byName('blue');
  print(c);                                 // Color.blue

  // Color.values.byName('purple');         // throws ArgumentError

  var map = Color.values.asNameMap();       // Map<String, Color>
  print(map['red']);                        // Color.red
  print(map['purple']);                     // null (safe lookup)
}
```

Use `asNameMap()` when the text might be invalid (for example, user input or JSON).

### Comparing

Enums are not ordered automatically, but you can compare by position:

```dart
var list = [Color.blue, Color.red, Color.green];
list.sort(Enum.compareByIndex);
print(list);   // [Color.red, Color.green, Color.blue]
```

### Summary

| Member | Type | Meaning |
|---|---|---|
| `name` | `String` | Identifier text of the value |
| `index` | `int` | Position, starting at 0 |
| `values` (static) | `List<E>` | All values in order |
| `values.byName(s)` | `E` | Lookup by text; throws if missing |
| `values.asNameMap()` | `Map<String, E>` | Name-to-value map for safe lookups |

---

[Previous](./[17]-Patterns-and-Destructuring.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[19]-Typedefs.md)
