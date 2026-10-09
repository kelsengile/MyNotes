[Previous](./[9]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[11]-Loops.md)

*Core Syntax*

# Lesson 10 - Conditionals: if, else, switch

Programs need to make decisions: show an error if a password is wrong, pick a discount based on the total, react differently to each kind of message. Conditionals let your code choose between paths. Dart offers `if`/`else`, the ternary operator, `switch` statements, the newer `switch` expressions, and `if-case`.

---

## 10.1 `if` and `else`

The `if` statement runs a block of code only when its condition is `true`. The condition **must be a `bool`** (Dart has no "truthiness", see Lesson 6).

```dart
void main() {
  var temperature = 32;

  if (temperature > 30) {
    print('It is hot.');
  }
}
```

Add `else` for the alternative, and `else if` to test further conditions in order. Only the **first** matching branch runs:

```dart
void main() {
  var score = 78;

  if (score >= 90) {
    print('Grade A');
  } else if (score >= 80) {
    print('Grade B');
  } else if (score >= 70) {
    print('Grade C');   // this one runs
  } else {
    print('Needs improvement');
  }
}
```

Combine conditions with `&&`, `||`, and `!`:

```dart
var age = 20;
var hasTicket = true;

if (age >= 18 && hasTicket) {
  print('Welcome in!');
}
```

If a branch has only one statement, the braces are optional, but the style guide recommends keeping them except for very short single-line `if` statements:

```dart
if (age < 0) print('Invalid age');
```

Nullable values must be checked before use; the check also promotes the type (Lesson 8):

```dart
String? name = 'Ana';
if (name != null) {
  print(name.toUpperCase());
}
```

---

## 10.2 The Ternary Operator

For a simple choice between two values, the **conditional operator** `condition ? valueIfTrue : valueIfFalse` is more compact than a full `if`/`else`. Unlike `if`, it is an **expression**: it produces a value.

```dart
void main() {
  var age = 17;
  var status = age >= 18 ? 'adult' : 'minor';
  print(status);   // minor

  print('You have ${3} item${3 == 1 ? '' : 's'}.');  // You have 3 items.
}
```

A related operator is `??` (if-null), which picks a fallback when a value is `null`:

```dart
String? nickname;
var display = nickname ?? 'Anonymous';
print(display);  // Anonymous
```

Avoid chaining many ternaries together, because they quickly become hard to read. Use `if`/`else` or a `switch` expression for three or more outcomes.

---

## 10.3 `switch` Statements

A `switch` statement compares one value against several **cases** and runs the first one that matches.

```dart
void main() {
  var command = 'open';

  switch (command) {
    case 'open':
      print('Opening...');
    case 'close':
      print('Closing...');
    case 'save':
    case 'save-all':
      print('Saving...');
    default:
      print('Unknown command');
  }
}
```

Important behaviour in Dart 3:

- Each non-empty case **ends automatically**. You do not write `break` (although you may).
- A case with an **empty body** falls through to the next one, so `'save'` and `'save-all'` above share the same code. You can also write this as `case 'save' || 'save-all':`.
- `default` (or `_`) handles every value not matched earlier.
- To jump to another case, use `continue` with a label (rarely needed).

Cases can match many kinds of values, including constants, ranges via patterns, and types:

```dart
void describe(Object value) {
  switch (value) {
    case 0:
      print('zero');
    case int n when n < 0:
      print('negative integer $n');
    case int n:
      print('positive integer $n');
    case String s when s.isEmpty:
      print('empty string');
    case String s:
      print('string of length ${s.length}');
    case _:
      print('something else');
  }
}

void main() {
  describe(0);        // zero
  describe(-4);       // negative integer -4
  describe(12);       // positive integer 12
  describe('');       // empty string
  describe('hello');  // string of length 5
  describe(3.5);      // something else
}
```

Here `int n` is a *type pattern* that also names the matched value, and `when` adds an extra condition (a **guard**). Patterns are explored fully in Lesson 17.

### Exhaustiveness

When you switch on an `enum`, a `bool`, or a `sealed` type (covered later), the compiler requires that **every possibility is handled**. If you forget one, you get an error, which protects you when new values are added later.

```dart
enum Light { red, yellow, green }

void act(Light light) {
  switch (light) {
    case Light.red:
      print('Stop');
    case Light.yellow:
      print('Slow down');
    case Light.green:
      print('Go');
    // No default needed: all enum values are covered
  }
}
```

---

## 10.4 `switch` Expressions

A **switch expression** produces a value, so it can be assigned, returned, or passed along. The syntax is more compact: no `case` keyword, no `break`, cases are separated by commas, and each case uses `=>`.

```dart
void main() {
  var day = 3;

  var name = switch (day) {
    1 => 'Monday',
    2 => 'Tuesday',
    3 => 'Wednesday',
    4 => 'Thursday',
    5 => 'Friday',
    6 || 7 => 'Weekend',
    _ => 'Invalid day',
  };

  print(name);   // Wednesday
}
```

Rules:

- A switch expression **must be exhaustive**. The wildcard `_` is the "everything else" case.
- Cases are checked from top to bottom; the first match wins.
- Use `||` to match several values, and `when` to add a condition.

Relational patterns make range checks very tidy:

```dart
String grade(int score) => switch (score) {
      >= 90 => 'A',
      >= 80 => 'B',
      >= 70 => 'C',
      >= 60 => 'D',
      _ => 'F',
    };

void main() {
  print(grade(95));  // A
  print(grade(72));  // C
  print(grade(10));  // F
}
```

With an enum, no wildcard is needed if every value is listed:

```dart
enum Size { small, medium, large }

double price(Size size) => switch (size) {
      Size.small => 2.50,
      Size.medium => 3.50,
      Size.large => 4.25,
    };
```

Switch expressions are usually the best choice when you are *computing a value* from a choice, while switch statements are for *performing actions*.

---

## 10.5 `if-case` Statements

An **if-case** combines an `if` with a pattern. It checks whether a value matches a pattern, and if so, pulls out parts of it into new variables that exist only inside the `if` block.

```dart
void main() {
  Object value = 42;

  if (value case int number) {
    print('Got an integer: $number');   // Got an integer: 42
  }
}
```

It is especially useful for checking and unpacking structured data such as maps from JSON:

```dart
void main() {
  var json = {'name': 'Ana', 'age': 21};

  if (json case {'name': String name, 'age': int age}) {
    print('$name is $age years old.');   // Ana is 21 years old.
  } else {
    print('Unexpected data');
  }
}
```

This single check confirms that the map has a `name` that is a `String` **and** an `age` that is an `int`, then gives you both values already typed. Without `if-case`, you would write several lookups, casts, and null checks.

You can add a guard with `when`:

```dart
void main() {
  var data = [1, 2, 3];

  if (data case [int first, ...] when first > 0) {
    print('Starts with a positive number: $first');
  }
}
```

`if-case` also works nicely with nullable values: a pattern like `final v?` matches only when the value is not null:

```dart
String? input = 'hello';

if (input case final text?) {
  print(text.length);   // text is a non-nullable String here
}
```

Use `if-case` when you want to test **and** extract in one step. For simple yes/no checks, a plain `if` is still clearer.

---

[Previous](./[9]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[11]-Loops.md)
