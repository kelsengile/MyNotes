[Previous](./[8]-Null-Safety.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[10]-Conditionals.md)

*Core Syntax*

# Lesson 9 - Operators & Expressions

An **expression** is a piece of code that produces a value, such as `3 + 4` or `name.length`. **Operators** are the symbols that combine values into expressions. This lesson tours all of Dart's operators and ends with the rules that decide which one runs first.

---

## 9.1 Arithmetic Operators

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `+` | Add | `7 + 2` | `9` |
| `-` | Subtract | `7 - 2` | `5` |
| `*` | Multiply | `7 * 2` | `14` |
| `/` | Divide (always a `double`) | `7 / 2` | `3.5` |
| `~/` | Integer divide | `7 ~/ 2` | `3` |
| `%` | Modulo (remainder) | `7 % 2` | `1` |
| `-expr` | Negate | `-(5)` | `-5` |

```dart
void main() {
  print(10 + 3);    // 13
  print(10 / 4);    // 2.5
  print(10 ~/ 4);   // 2
  print(10 % 4);    // 2
  print(-7 ~/ 2);   // -3  (truncates toward zero)
  print(-7 % 3);    // 2   (modulo is never negative)
}
```

### Increment and decrement

`++` and `--` add or subtract 1. Written **before** the variable (prefix), the change happens first. Written **after** (postfix), the old value is used first:

```dart
void main() {
  var a = 5;
  print(a++);   // 5  (prints old value, then a becomes 6)
  print(a);     // 6

  var b = 5;
  print(++b);   // 6  (b becomes 6, then prints)
  print(b--);   // 6  (prints, then b becomes 5)
  print(b);     // 5
}
```

The `+` operator also joins strings (`'a' + 'b'`) and lists (`[1] + [2]` gives `[1, 2]`).

---

## 9.2 Equality and Relational Operators

| Operator | Meaning |
|---|---|
| `==` | Equal |
| `!=` | Not equal |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |

All of them produce a `bool`:

```dart
void main() {
  print(5 == 5);        // true
  print(5 != 3);        // true
  print(5 > 8);         // false
  print(5 <= 5);        // true
  print('a' == 'a');    // true  (compares contents)
  print(null == null);  // true
}
```

### Equality for objects

For most objects, `==` checks whether two objects are the *same object* unless the class defines its own meaning of equality (strings, numbers, and many built-in types do):

```dart
void main() {
  print([1, 2] == [1, 2]);          // false: two different list objects
  print(identical(1, 1));           // true
  var l = [1, 2];
  var m = l;
  print(l == m);                    // true: same object
  print(identical(l, m));           // true
}
```

`identical(a, b)` checks whether two references point to the very same object. Comparing lists by content, and defining equality for your own classes, are covered in later lessons.

Relational operators (`<`, `>`, ...) only work between numbers (and types that define them). Comparing a `String` to an `int` with `<` is a compile-time error.

---

## 9.3 Logical Operators

| Operator | Meaning |
|---|---|
| `!` | NOT: reverses a boolean |
| `&&` | AND: true only if both sides are true |
| `\|\|` | OR: true if at least one side is true |

```dart
void main() {
  var age = 20;
  var hasId = true;

  print(age >= 18 && hasId);      // true
  print(age < 13 || age > 65);    // false
  print(!hasId);                  // false
}
```

### Short-circuit evaluation

`&&` and `||` stop as soon as the result is known, and do not evaluate the right side unnecessarily:

```dart
bool check(String label) {
  print('checking $label');
  return true;
}

void main() {
  var result = false && check('A');  // check('A') is never called
  var other = true || check('B');    // check('B') is never called
  print('$result $other');           // false true
}
```

This is useful for safe checks, such as `list.isNotEmpty && list.first > 0`: the second part only runs if the list has elements.

---

## 9.4 Bitwise and Shift Operators

These operators work on the individual **bits** of integers. They are used for flags, masks, and low-level data work.

| Operator | Meaning | Example (`a = 0b1100`, `b = 0b1010`) |
|---|---|---|
| `&` | AND | `a & b` → `0b1000` (8) |
| `\|` | OR | `a \| b` → `0b1110` (14) |
| `^` | XOR (different bits) | `a ^ b` → `0b0110` (6) |
| `~` | NOT (flip all bits) | `~a` → `-13` |
| `<<` | Shift left | `1 << 3` → `8` |
| `>>` | Shift right (keeps sign) | `16 >> 2` → `4` |
| `>>>` | Unsigned shift right | `-1 >>> 60` → `15` |

```dart
void main() {
  var a = 12;  // 1100
  var b = 10;  // 1010
  print(a & b);      // 8
  print(a | b);      // 14
  print(a ^ b);      // 6
  print(1 << 4);     // 16
  print(256 >> 2);   // 64
}
```

A practical use is storing several on/off options in one integer:

```dart
const read = 1;    // 001
const write = 2;   // 010
const run = 4;     // 100

void main() {
  var permissions = read | write;               // 011
  print(permissions & write != 0);              // true: has write
  print(permissions & run != 0);                // false: no run
}
```

> The `&` operator has *lower* precedence than `!=` in some languages, but in Dart bitwise AND binds **tighter** than `==`/`!=`, so the example above works without extra parentheses. Adding parentheses is still a good habit for readability.

The `>>>` operator has been available since Dart 2.14.

---

## 9.5 Assignment and Compound Assignment

The basic assignment operator is `=`. **Compound** operators combine an operation with assignment:

| Operator | Same as |
|---|---|
| `a += b` | `a = a + b` |
| `a -= b` | `a = a - b` |
| `a *= b` | `a = a * b` |
| `a /= b` | `a = a / b` |
| `a ~/= b` | `a = a ~/ b` |
| `a %= b` | `a = a % b` |
| `a <<= b`, `a >>= b` | shifts, then assign |
| `a &= b`, `a \|= b`, `a ^= b` | bitwise, then assign |
| `a ??= b` | assign only if `a` is null |

```dart
void main() {
  var total = 10;
  total += 5;     // 15
  total *= 2;     // 30
  total -= 10;    // 20
  total ~/= 3;    // 6
  total %= 4;     // 2
  print(total);   // 2

  String? label;
  label ??= 'untitled';
  print(label);   // untitled
}
```

`/=` produces a `double`, so it only works on a variable that can hold one (`double` or `num`):

```dart
var d = 10.0;
d /= 4;
print(d); // 2.5
```

---

## 9.6 Type Test Operators (`is`, `is!`, `as`)

| Operator | Meaning |
|---|---|
| `is` | True if the object has the given type |
| `is!` | True if the object does **not** have the given type |
| `as` | Cast: treat the object as the given type (throws if wrong) |

```dart
void main() {
  Object value = 'hello';

  print(value is String);   // true
  print(value is int);      // false
  print(value is! int);     // true

  if (value is String) {
    print(value.length);    // value is promoted to String, no cast needed
  }

  var text = value as String;   // OK
  print(text.toUpperCase());

  // var number = value as int; // throws a TypeError at run time
}
```

Prefer `is` (with promotion) over `as` whenever you are not certain of the type, because a failed `as` crashes the program. Also note that every object `is Object`, and a non-null object `is Object?`.

`is` works with built-in generic types too:

```dart
Object items = [1, 2, 3];
print(items is List);        // true
print(items is List<int>);   // true
print(items is List<String>); // false
```

---

## 9.7 The Cascade Operator (`..` and `?..`)

The **cascade** operator lets you perform several operations on the *same object* without repeating its name. Each `..` runs on the original object, and the whole expression returns that object.

Without a cascade:

```dart
var buffer = StringBuffer();
buffer.write('Hello');
buffer.write(', ');
buffer.write('Dart');
buffer.writeln('!');
```

With a cascade:

```dart
var buffer = StringBuffer()
  ..write('Hello')
  ..write(', ')
  ..write('Dart')
  ..writeln('!');
```

Cascades can also set properties, which is common when configuring objects:

```dart
class Person {
  String name = '';
  int age = 0;
  void greet() => print('Hi, I am $name ($age).');
}

void main() {
  var p = Person()
    ..name = 'Ana'
    ..age = 21
    ..greet();     // Hi, I am Ana (21).
}
```

Why not just use `.`? A normal method call returns the method's own result, but a cascade always gives back the object, so you can keep going and still store the object in one variable.

### Null-aware cascade `?..`

If the object might be null, start the cascade with `?..`. If the object is null, the entire cascade is skipped. Later parts of the same cascade use plain `..`:

```dart
void main() {
  StringBuffer? maybe;
  maybe
    ?..write('one')
    ..write('two');    // skipped entirely because maybe is null

  maybe = StringBuffer();
  maybe
    ?..write('one')
    ..write('two');
  print(maybe);        // onetwo
}
```

---

## 9.8 Operator Precedence

When an expression has several operators, **precedence** decides which runs first (like multiplication before addition in math). From highest (first) to lowest (last):

| Level | Operators |
|---|---|
| Postfix | `expr++` `expr--` `()` `[]` `.` `?.` `!` |
| Prefix (unary) | `-expr` `!expr` `~expr` `++expr` `--expr` |
| Multiplicative | `*` `/` `%` `~/` |
| Additive | `+` `-` |
| Shift | `<<` `>>` `>>>` |
| Bitwise AND | `&` |
| Bitwise XOR | `^` |
| Bitwise OR | `\|` |
| Relational and type test | `>=` `>` `<=` `<` `as` `is` `is!` |
| Equality | `==` `!=` |
| Logical AND | `&&` |
| Logical OR | `\|\|` |
| If-null | `??` |
| Conditional | `expr1 ? expr2 : expr3` |
| Cascade | `..` `?..` |
| Assignment | `=` `*=` `+=` `??=` and the other compound forms |

Examples:

```dart
void main() {
  print(2 + 3 * 4);       // 14, because * runs before +
  print((2 + 3) * 4);     // 20, parentheses override precedence
  print(10 - 4 - 3);      // 3, same level runs left to right
  print(true || false && false);  // true, && runs before ||
  print(1 + 2 < 5 && 4 > 3);      // true
  print(null ?? 1 + 2);           // 3, + runs before ??
}
```

Operators on the same level are evaluated **left to right**, except assignment and the conditional operator, which group right to left.

> **Tip:** When in doubt, add parentheses. Clear code is better than code that relies on remembering the table.

---

[Previous](./[8]-Null-Safety.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[10]-Conditionals.md)
