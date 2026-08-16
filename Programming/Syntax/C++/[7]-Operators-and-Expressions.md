[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators And Expressions

## 7.1 Arithmetic Operators

```cpp
int a = 10, b = 3;

int sum = a + b;        // 13
int diff = a - b;       // 7
int product = a * b;    // 30
int quotient = a / b;   // 3  (integer division truncates)
int remainder = a % b;  // 1  (modulo)

double preciseDiv = 10.0 / 3.0; // 3.3333...
```

Note that dividing two integers always produces an integer result — the fractional part is discarded, not rounded. If you need a precise result, make sure at least one operand is a floating-point type.

---

## 7.2 Comparison Operators

Comparison operators evaluate to a `bool`:

```cpp
a == b   // equal to
a != b   // not equal to
a > b    // greater than
a < b    // less than
a >= b   // greater than or equal to
a <= b   // less than or equal to
```

A common beginner mistake is writing `=` (assignment) instead of `==` (comparison) inside a condition — most compilers will warn about this if you enable `-Wall`.

---

## 7.3 Logical Operators

Logical operators combine or invert boolean expressions:

```cpp
bool isAdult = true;
bool hasTicket = false;

bool canEnter = isAdult && hasTicket; // AND: both must be true
bool needsHelp = isAdult || hasTicket; // OR: at least one is true
bool isMinor = !isAdult;               // NOT: inverts the value
```

C++ uses **short-circuit evaluation**: in `a && b`, if `a` is `false`, `b` is never evaluated, since the result is already known. This is useful for safely guarding expressions:

```cpp
if (ptr != nullptr && ptr->value > 0) {
    // safe: ptr->value is only checked if ptr isn't null
}
```

---

## 7.4 Bitwise Operators

Bitwise operators work on the individual bits of integer values:

```cpp
unsigned int x = 0b1010; // 10
unsigned int y = 0b0110; // 6

x & y;   // AND:  0b0010 (2)
x | y;   // OR:   0b1110 (14)
x ^ y;   // XOR:  0b1100 (12)
~x;      // NOT:  flips every bit
x << 1;  // shift left:  0b10100 (20)
x >> 1;  // shift right: 0b0101  (5)
```

These are common in low-level code: working with flags, hardware registers, or performance-critical bit manipulation.

---

## 7.5 Operator Precedence & Assignment Operators

Like in math, some operators bind tighter than others. Multiplication happens before addition unless parentheses say otherwise:

```cpp
int result = 2 + 3 * 4;   // 14, not 20
int forced = (2 + 3) * 4; // 20
```

C++ also provides **compound assignment operators** as shorthand:

```cpp
int score = 10;
score += 5;  // same as score = score + 5;
score -= 2;  // same as score = score - 2;
score *= 3;  // same as score = score * 3;
score /= 2;  // same as score = score / 2;
```

When precedence isn't immediately obvious, adding parentheses makes the intent clear to future readers — including yourself.

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[8]-Conditionals.md)
