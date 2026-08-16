[Previous](./[5]-Numbers-and-Characters.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[7]-Conditionals.md)

*Core Syntax*

# Lesson 6 - Operators And Expressions

## 6.1 Arithmetic Operators

```c
int a = 10, b = 3;

a + b;   // 13  addition
a - b;   // 7   subtraction
a * b;   // 30  multiplication
a / b;   // 3   integer division (truncates toward zero)
a % b;   // 1   modulo (remainder)
```

Remember: dividing two integers always produces an integer result, discarding any remainder. To get a fractional result, at least one operand must be a floating-point type (see Lesson 5.5).

---

## 6.2 Comparison Operators

Comparisons produce an `int`: `1` for true, `0` for false.

```c
a == b;   // equal to
a != b;   // not equal to
a > b;    // greater than
a < b;    // less than
a >= b;   // greater than or equal to
a <= b;   // less than or equal to
```

A classic beginner mistake is writing `=` (assignment) instead of `==` (comparison) inside a condition:

```c
if (a = 5) { ... }   // BUG: assigns 5 to a, which is always "true"
if (a == 5) { ... }  // correct: compares a to 5
```

Compiling with `-Wall` (Lesson 3.3) will flag this as a likely mistake.

---

## 6.3 Logical Operators

```c
a && b;   // logical AND: true if both are true
a || b;   // logical OR: true if either is true
!a;       // logical NOT: inverts true/false
```

C uses **short-circuit evaluation**: in `a && b`, if `a` is false, `b` is never evaluated, because the result is already determined. This is often used to guard against invalid operations:

```c
if (ptr != NULL && ptr->value > 0) {
    // safe: ptr->value is only checked if ptr isn't NULL
}
```

---

## 6.4 Bitwise Operators

Bitwise operators act on the individual bits of integer values:

```c
a & b;    // AND
a | b;    // OR
a ^ b;    // XOR
~a;       // NOT (bitwise complement)
a << 2;   // shift left by 2 bits (multiplies by 4)
a >> 2;   // shift right by 2 bits (divides by 4, for unsigned/non-negative values)
```

These are common in systems programming for flags, masks, and low-level manipulation:

```c
#define FLAG_READ  0x1
#define FLAG_WRITE 0x2

int permissions = FLAG_READ | FLAG_WRITE;   // combine flags
if (permissions & FLAG_WRITE) {             // check a flag
    printf("write allowed\n");
}
```

Don't confuse `&&`/`||` (logical, work on truth values) with `&`/`|` (bitwise, work on individual bits) — mixing them up is a common bug.

---

## 6.5 Assignment and Compound Assignment

```c
int x = 5;
x += 3;   // x = x + 3   -> 8
x -= 2;   // x = x - 2   -> 6
x *= 4;   // x = x * 4   -> 24
x /= 3;   // x = x / 3   -> 8
x %= 5;   // x = x % 5   -> 3

x++;      // post-increment: x = x + 1
++x;      // pre-increment: x = x + 1
x--;      // post-decrement
--x;      // pre-decrement
```

`x++` and `++x` both increment `x`, but differ when used as part of a larger expression: `x++` returns the value *before* incrementing, while `++x` returns the value *after*.

```c
int a = 5;
int b = a++;  // b = 5, a becomes 6
int c = ++a;  // a becomes 7, c = 7
```

---

## 6.6 Operator Precedence

Like arithmetic in math, C operators have precedence rules deciding what evaluates first:

```c
int result = 2 + 3 * 4;   // 14, not 20 -- multiplication binds tighter than addition
int result2 = (2 + 3) * 4; // 20 -- parentheses override precedence
```

When precedence isn't obvious at a glance, use parentheses — it costs nothing and removes any doubt for whoever reads the code next, including future you.

---

[Previous](./[5]-Numbers-and-Characters.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[7]-Conditionals.md)
