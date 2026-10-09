
[Previous](./[16]-Records.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[18]-Enums.md)

*Core Syntax*

# Lesson 17 - Patterns & Destructuring

A **pattern** describes the *shape* of a value. Dart can compare a value against a pattern to see whether it fits, and can pull pieces out of it into variables at the same time. Patterns are one of Dart 3's biggest features, and they make code that handles structured data much shorter and safer.

> Patterns require Dart 3.0 or later.

---

## 17.1 What Are Patterns?

A pattern does up to two jobs:

1. **Match:** test whether a value has a certain shape, type, or value.
2. **Destructure:** extract parts of the value into new variables.

Here is the same task without and with a pattern:

```dart
void main() {
  var point = (3, 4);

  // Without a pattern
  var x1 = point.$1;
  var y1 = point.$2;

  // With a pattern (destructuring)
  var (x, y) = point;
  print('$x, $y');   // 3, 4
}
```

### Where patterns can be used

| Context | Example |
|---|---|
| Variable declaration | `var (a, b) = (1, 2);` |
| Assignment | `(a, b) = (b, a);` |
| `switch` statement and expression cases | `case [int a, int b]:` |
| `if-case` | `if (value case int n) { ... }` |
| `for-in` loops | `for (var (k, v) in pairs) { ... }` |
| Collection `if` and `for` | `[if (v case int n) n]` |

### Kinds of patterns at a glance

| Pattern | Looks like | Matches |
|---|---|---|
| Constant | `42`, `'hi'`, `null`, `Color.red` | That exact value |
| Variable | `var x`, `final x`, `int x` | Anything (of that type); binds it to `x` |
| Wildcard | `_` | Anything; ignores it |
| List | `[a, b, ...]` | A list of that shape |
| Map | `{'key': v}` | A map that has those keys |
| Record | `(a, b)`, `(x: var x)` | A record of that shape |
| Object | `Point(x: var px)` | An instance of that class |
| Logical-or / and | `a \|\| b`, `a && b` | Either / both patterns |
| Relational | `> 5`, `== 0` | Values that satisfy the comparison |
| Cast | `x as int` | Casts, throws if wrong |
| Null-check / assert | `x?`, `x!` | Non-null values |

The rest of this lesson covers each one.

### Refutable and irrefutable patterns

- A pattern that **might fail** to match is **refutable**. These are used in `switch`, `if-case`, where failing simply moves on to the next case.
- A pattern that **must** match is **irrefutable**. Declarations and assignments require these; if the value does not fit, a run-time error is thrown.

```dart
var (a, b) = (1, 2);          // always fits: fine
// var (c, d) = (1, 2, 3);    // ERROR: record shape does not match
```

---

## 17.2 Destructuring Lists, Maps, and Records

### Records

```dart
void main() {
  var (name, age) = ('Ana', 21);              // positional
  print('$name is $age');                     // Ana is 21

  var (x: px, y: py) = (x: 3, y: 4);          // named: field: variable
  print('$px $py');                           // 3 4

  var (:x, :y) = (x: 5, y: 6);                // shorthand: variables named after the fields
  print('$x $y');                             // 5 6
}
```

### Lists

A list pattern lists one subpattern per element. It matches only if the list has **exactly** that many elements, unless you use a rest element `...`:

```dart
void main() {
  var [first, second] = [10, 20];
  print('$first $second');           // 10 20

  var [head, ...tail] = [1, 2, 3, 4];
  print(head);                       // 1
  print(tail);                       // [2, 3, 4]

  var [a, ..., z] = [1, 2, 3, 4, 5];  // a bare ... skips the middle
  print('$a $z');                    // 1 5

  var [p, ...middle, q] = [1, 2, 3, 4];
  print(middle);                     // [2, 3]
}
```

If the list is the wrong length for the pattern, a declaration throws an error. In a `switch`, the case simply does not match:

```dart
String describe(List<int> list) {
  switch (list) {
    case []:
      return 'empty';
    case [var only]:
      return 'one item: $only';
    case [var a, var b]:
      return 'two items: $a and $b';
    case [var first, ...var rest]:
      return 'starts with $first, then ${rest.length} more';
  }
}

void main() {
  print(describe([]));            // empty
  print(describe([7]));           // one item: 7
  print(describe([1, 2]));        // two items: 1 and 2
  print(describe([1, 2, 3, 4]));  // starts with 1, then 3 more
}
```

(The switch above is exhaustive because the last case accepts any non-empty list.)

### Maps

A map pattern lists keys (which must be constants) and a subpattern for each value. The map may contain **extra** keys; they are ignored. If a listed key is missing, the pattern does not match.

```dart
void main() {
  var user = {'name': 'Ana', 'age': 21, 'city': 'Manila'};

  var {'name': name, 'age': age} = user;
  print('$name, $age');   // Ana, 21

  if (user case {'city': String city}) {
    print('Lives in $city');   // Lives in Manila
  }
}
```

### Nesting

Patterns can be nested to any depth:

```dart
void main() {
  var order = (id: 7, items: ['pen', 'ink'], customer: (name: 'Ana', vip: true));

  var (id: id, items: [firstItem, ...], customer: (name: who, vip: vip)) = order;
  print('$id $firstItem $who $vip');   // 7 pen Ana true
}
```

### Other places destructuring helps

```dart
void main() {
  // Swap without a temporary variable
  var a = 1, b = 2;
  (a, b) = (b, a);
  print('$a $b');   // 2 1

  // In a for-in loop over map entries
  var ages = {'Ana': 21, 'Ben': 19};
  for (final MapEntry(key: name, value: age) in ages.entries) {
    print('$name: $age');
  }

  // Ignoring parts with the wildcard _
  var (_, second, _) = (1, 2, 3);
  print(second);    // 2
}
```

---

## 17.3 Object Patterns

An **object pattern** checks that a value is an instance of a class, and then matches against that object's **getters**. The syntax is the class name followed by parentheses listing `getterName: pattern`.

```dart
class Point {
  final double x;
  final double y;
  const Point(this.x, this.y);
}

String where(Object shape) {
  switch (shape) {
    case Point(x: 0, y: 0):
      return 'the origin';
    case Point(x: var px, y: 0):
      return 'on the x-axis at $px';
    case Point(:var x, :var y):         // shorthand: bind variables named x and y
      return 'at ($x, $y)';
    default:
      return 'not a point';
  }
}

void main() {
  print(where(const Point(0, 0)));   // the origin
  print(where(const Point(5, 0)));   // on the x-axis at 5.0
  print(where(const Point(2, 3)));   // at (2.0, 3.0)
  print(where('hello'));             // not a point
}
```

Notes:

- `Point()` with empty parentheses simply tests the type: `case Point():` matches any `Point`.
- Object patterns work with **any class**, because they read getters; no special setup is needed.
- A very common use is unpacking a class hierarchy (see sealed classes in Lesson 25):

```dart
sealed class Shape {}
class Circle extends Shape { final double radius; Circle(this.radius); }
class Rect extends Shape { final double w, h; Rect(this.w, this.h); }

double area(Shape s) => switch (s) {
      Circle(radius: var r) => 3.14159 * r * r,
      Rect(:var w, :var h) => w * h,
    };
```

---

## 17.4 Logical, Relational, and Cast Patterns

### Logical-or `||`

Matches if **either** side matches. Both sides must bind the same set of variables:

```dart
String kind(int n) => switch (n) {
      1 || 2 || 3 => 'small',
      4 || 5 || 6 => 'medium',
      _ => 'large',
    };
```

```dart
void main() {
  // Same variable bound on both sides of ||
  var list = [0, 9];
  if (list case [var v, 0] || [0, var v]) {
    print('found $v next to a zero');   // found 9 next to a zero
  }
}
```

### Logical-and `&&`

Matches only if **both** sides match:

```dart
String check(int n) => switch (n) {
      > 0 && < 10 => 'one digit, positive',
      >= 10 && < 100 => 'two digits',
      _ => 'other',
    };
```

### Relational patterns

These compare the value using `==`, `!=`, `<`, `>`, `<=`, `>=` against a constant:

```dart
String grade(int score) => switch (score) {
      >= 90 => 'A',
      >= 80 => 'B',
      >= 70 => 'C',
      _ => 'F',
    };
```

### Cast pattern `as`

A cast pattern converts the value to a type and throws a `TypeError` if it is not of that type. It is mainly useful inside destructuring when you know a type:

```dart
void main() {
  (Object, Object) pair = (1, 'one');
  var (number as int, word as String) = pair;
  print(number + 1);        // 2
  print(word.length);       // 3
}
```

### Null-check `?` and null-assert `!`

```dart
void main() {
  String? maybe = 'hello';

  // x? matches only non-null values
  if (maybe case var text?) {
    print(text.length);       // text is a non-nullable String
  }

  // x! asserts non-null (throws if null)
  List<int?> values = [1, 2];
  var [a!, b!] = values;
  print(a + b);               // 3
}
```

### Variable patterns with types

A type in front of a variable name restricts the match to that type:

```dart
String typeOf(Object o) => switch (o) {
      int n => 'int $n',
      String s => 'string "$s"',
      List l => 'list of ${l.length}',
      _ => 'unknown',
    };
```

---

## 17.5 Guard Clauses (`when`)

Sometimes a pattern alone is not enough, and you need an extra condition that uses the bound variables. Add a **guard** with `when` after the pattern. The case matches only if the pattern matches **and** the guard is `true`:

```dart
String describe(Object value) => switch (value) {
      int n when n < 0 => 'negative',
      int n when n == 0 => 'zero',
      int n when n.isEven => 'positive even',
      int _ => 'positive odd',
      String s when s.isEmpty => 'empty string',
      _ => 'something else',
    };

void main() {
  print(describe(-5));    // negative
  print(describe(0));     // zero
  print(describe(8));     // positive even
  print(describe(7));     // positive odd
  print(describe(''));    // empty string
}
```

Guards work in `switch` statements and `if-case` too:

```dart
void main() {
  var pair = (3, 9);

  if (pair case (var a, var b) when a < b) {
    print('$a is less than $b');   // 3 is less than 9
  }
}
```

Important points:

- Cases are tried **in order**, so put the more specific cases first.
- If a guard is `false`, matching simply continues with the next case.
- A guarded case does **not** count toward exhaustiveness (the compiler cannot know when the guard will be true), so you still need a case that covers everything else.

---

## 17.6 Exhaustiveness Checking

A `switch` over certain types must handle **every possible value**. The compiler checks this for you, and reports an error if a case is missing. Types that are checked this way include:

- `enum` types
- `sealed` class hierarchies (Lesson 25)
- `bool`
- `Null` and nullable types (the `null` case must be handled)
- records and patterns built from the types above

### Example with an enum

```dart
enum Direction { north, south, east, west }

String arrow(Direction d) => switch (d) {
      Direction.north => 'up',
      Direction.south => 'down',
      Direction.east => 'right',
      Direction.west => 'left',
    };
```

If you later add `Direction.up` to the enum, every `switch` like this one stops compiling until you handle it. That is the real value: **the compiler finds all the places that need updating.**

Forgetting a case gives an error such as: *"The type 'Direction' is not exhaustively matched by the switch cases"*.

### Combinations are checked too

```dart
String describe(bool a, bool b) => switch ((a, b)) {
      (true, true) => 'both',
      (true, false) => 'only a',
      (false, true) => 'only b',
      (false, false) => 'neither',
    };
```

### Using `_` as the catch-all

When you intentionally want "everything else", use the wildcard `_` (or `default` in a statement). This satisfies the check, but it also **silences future warnings** when new values are added, so avoid it when you want the compiler's help:

```dart
String isNorth(Direction d) => switch (d) {
      Direction.north => 'yes',
      _ => 'no',
    };
```

### Other checks

- **Unreachable cases:** If an earlier case already covers a later one, the analyzer warns that the later case can never match.
- **Statements:** An exhaustive `switch` *statement* on an enum or sealed type must be exhaustive; a switch statement on `int` or `String` need not be (the plain `default` is optional).
- **`if-case`:** is never required to be exhaustive, since it has an implicit "otherwise" (the `else`).

---

[Previous](./[16]-Records.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[18]-Enums.md)
