[Previous](./[5]-Variables-and-Type-Inference.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[7]-Strings.md)

*Core Syntax*

# Lesson 6 - Built-in Types: Numbers & Booleans

Numbers and true/false values are the foundation of calculations and decisions. In this lesson you will learn Dart's number types, how to convert text to numbers and back, and how booleans work (including a rule that surprises people coming from other languages).

---

## 6.1 `int` and `double`

Dart has two main number types:

- **`int`** holds whole numbers: `-3`, `0`, `42`.
- **`double`** holds numbers with a fractional part: `3.14`, `-0.5`, `2.0`.

```dart
void main() {
  int apples = 12;
  double price = 2.75;
  print(apples * price); // 33.0
}
```

### Literals

```dart
var decimal = 255;
var hex = 0xFF;          // 255 (hexadecimal)
var big = 1000000;
var exp = 1.5e3;         // 1500.0 (scientific notation: a double)
var small = 1e-3;        // 0.001
```

Note that `1e3` is a **double** even though it has no decimal point. An `int` literal can be used where a `double` is expected and is converted automatically:

```dart
double d = 5;   // OK: d is 5.0
print(d);       // 5.0
```

The reverse is *not* allowed: a `double` cannot be assigned to an `int` variable without converting it (see 6.3).

### Division

The `/` operator **always returns a double**, even for whole numbers. For integer division use `~/`:

```dart
print(7 / 2);   // 3.5
print(8 / 2);   // 4.0
print(7 ~/ 2);  // 3   (integer result, rounded toward zero)
print(7 % 2);   // 1   (remainder)
```

### Size and precision

- On the **Dart VM** (native), an `int` is a 64-bit signed integer: from -2<sup>63</sup> to 2<sup>63</sup> - 1.
- When compiled to **JavaScript**, numbers are JavaScript numbers, so integers are only exact up to 2<sup>53</sup>. If you need very large integers on every platform, use `BigInt` (covered in the `dart:core` lesson).
- A `double` is a 64-bit IEEE 754 floating-point value, so some decimals cannot be stored exactly:

```dart
print(0.1 + 0.2); // 0.30000000000000004
```

This is normal for floating-point numbers in every language. Do not compare doubles with `==` after calculations; compare whether they are *close enough*, or round them first.

---

## 6.2 `num`

`num` is the parent type of both `int` and `double`. Use it when a value can be either:

```dart
num x = 5;      // holds an int
x = 2.5;        // now holds a double: allowed because both are num
print(x);       // 2.5
```

A function that works with any number can accept `num`:

```dart
num doubleIt(num value) => value * 2;

void main() {
  print(doubleIt(4));    // 8
  print(doubleIt(1.5));  // 3.0
}
```

You can check which kind it currently holds with `is`:

```dart
num n = 7;
if (n is int) {
  print('whole number');
} else {
  print('fractional number');
}
```

Prefer the specific type (`int` or `double`) whenever you know it, because it gives you more precise type checking.

---

## 6.3 Parsing and Converting Numbers

### String to number

Use `parse` to turn text into a number. It throws a `FormatException` if the text is not a valid number:

```dart
void main() {
  int a = int.parse('42');
  double b = double.parse('3.14');
  print(a + 1);  // 43
  print(b * 2);  // 6.28

  // int.parse('abc'); // throws FormatException
}
```

Use `tryParse` when the input may be invalid. It returns `null` instead of throwing:

```dart
void main() {
  print(int.tryParse('123'));    // 123
  print(int.tryParse('12.5'));   // null (not a valid int)
  print(int.tryParse('hello'));  // null

  final input = 'oops';
  final value = int.tryParse(input) ?? 0; // fall back to 0
  print(value); // 0
}
```

`int.parse` also supports other number bases:

```dart
print(int.parse('ff', radix: 16));   // 255
print(int.parse('1010', radix: 2));  // 10
```

### Number to string

```dart
void main() {
  print(42.toString());                 // "42"
  print(3.14159.toStringAsFixed(2));    // "3.14" (exactly 2 decimal places)
  print(255.toRadixString(16));         // "ff"
  print(255.toRadixString(2));          // "11111111"
  print(5.toString().padLeft(3, '0'));  // "005"
}
```

### Between `int` and `double`

```dart
void main() {
  double d = 7.8;
  print(d.toInt());    // 7  (drops the fraction)
  print(d.round());    // 8  (nearest whole number, returns int)
  print(d.floor());    // 7  (round down)
  print(d.ceil());     // 8  (round up)
  print(d.truncate()); // 7  (toward zero)

  int i = 3;
  print(i.toDouble()); // 3.0
}
```

Note that `round()`, `floor()`, `ceil()`, and `truncate()` all return an `int`. Use `roundToDouble()` and friends if you need a `double` result.

---

## 6.4 `bool` and Truthiness Rules

A `bool` has exactly two possible values: `true` and `false`.

```dart
void main() {
  bool isOpen = true;
  bool hasTicket = false;

  print(isOpen && hasTicket); // false
  print(isOpen || hasTicket); // true
  print(!isOpen);             // false
}
```

Comparison operators produce booleans:

```dart
print(5 > 3);    // true
print(5 == 5);   // true
print(5 != 5);   // false
```

### No "truthiness" in Dart

In languages like JavaScript or Python, values such as `0`, `''`, or an empty list can act as "false" in a condition. **Dart does not do this.** A condition must be an actual `bool`:

```dart
var count = 3;

// if (count) { ... }     // ERROR: 'int' can't be used as a condition
if (count != 0) {         // OK: explicit comparison produces a bool
  print('not zero');
}

var name = '';
// if (name) { ... }      // ERROR
if (name.isEmpty) {       // OK
  print('no name');
}
```

The same applies to `null`: you cannot use a nullable value directly as a condition. Compare it explicitly (`value != null`), as covered in the null safety lesson.

This strictness removes a whole category of bugs where a value is accidentally "falsy".

---

## 6.5 Number Methods and `dart:math`

### Useful members on numbers

```dart
void main() {
  print((-5).abs());        // 5
  print(10.isEven);         // true
  print(7.isOdd);           // true
  print((-3).isNegative);   // true
  print(15.clamp(0, 10));   // 10 (limits the value to a range)
  print((-7) % 3);          // 2  (% result is never negative)
  print((-7).remainder(3)); // -1 (keeps the sign of the dividend)
  print(12.gcd(18));        // 6  (greatest common divisor)
  print(double.nan.isNaN);  // true
  print(5.compareTo(8));    // -1 (less than)
}
```

### The `dart:math` library

More advanced math lives in the `dart:math` library, which you must import. Using a prefix keeps names tidy:

```dart
import 'dart:math' as math;

void main() {
  print(math.pi);              // 3.141592653589793
  print(math.e);               // 2.718281828459045
  print(math.sqrt(16));        // 4.0
  print(math.pow(2, 10));      // 1024
  print(math.max(3, 9));       // 9
  print(math.min(3, 9));       // 3
  print(math.sin(math.pi / 2)); // 1.0
  print(math.log(math.e));     // 1.0 (natural logarithm)
}
```

Notes:

- `sqrt` always returns a `double`.
- `pow(a, b)` returns a `num`; with integer arguments and a non-negative integer exponent the result is an `int`.
- `Random` for random numbers is also in `dart:math`. It is covered in detail in Lesson 40.

---

[Previous](./[5]-Variables-and-Type-Inference.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[7]-Strings.md)
