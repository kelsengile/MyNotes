[Previous](./[4]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[6]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 5 - Integers, Floats, And Chars

## 5.1 Integer Types and Signedness

C offers several integer types of different sizes:

```c
short   s = 10;      // at least 16 bits
int     i = 1000;     // at least 16 bits, typically 32
long    l = 100000L;  // at least 32 bits
long long ll = 10000000000LL;  // at least 64 bits
```

Each also has an `unsigned` counterpart, which drops the sign to represent only non-negative numbers, roughly doubling the maximum positive value:

```c
unsigned int count = 4000000000u;
```

Signed integers use two's complement representation. Overflowing a signed integer (exceeding its max value) is **undefined behavior**; overflowing an unsigned integer instead wraps around predictably (e.g. `UINT_MAX + 1` becomes `0`).

```c
#include <limits.h>
printf("%d\n", INT_MAX);   // largest value an int can hold
```

---

## 5.2 Floating-Point Types

`float` and `double` store approximate real numbers using the IEEE 754 standard:

```c
float  f = 3.14f;         // ~7 significant decimal digits
double d = 3.14159265358979;  // ~15-16 significant decimal digits
```

Because floats are stored in binary, many decimal values (like `0.1`) can't be represented exactly:

```c
if (0.1 + 0.2 == 0.3) {  // false! rounding error
    printf("equal\n");
}
```

Never compare floating-point numbers with `==`. Instead, check that the difference is within a small tolerance:

```c
#include <math.h>
if (fabs((0.1 + 0.2) - 0.3) < 1e-9) {
    printf("close enough\n");
}
```

---

## 5.3 The char Type

`char` stores a single byte, typically interpreted as an ASCII character:

```c
char letter = 'A';
printf("%c has code %d\n", letter, letter);  // A has code 65
```

Since `char` is really a small integer under the hood, you can do arithmetic on it:

```c
char next = 'A' + 1;  // 'B'
```

Whether plain `char` is signed or unsigned is implementation-defined — if you need guaranteed signedness, use `signed char` or `unsigned char` explicitly.

---

## 5.4 Type Sizes and sizeof

Exact type sizes vary by platform and compiler. Use the `sizeof` operator to check at compile time rather than assuming:

```c
#include <stdio.h>

int main(void) {
    printf("int: %zu bytes\n", sizeof(int));
    printf("double: %zu bytes\n", sizeof(double));
    printf("char: %zu bytes\n", sizeof(char));
    return 0;
}
```

`sizeof` returns a `size_t`, an unsigned type — always print it with `%zu`, not `%d`.

For guaranteed, fixed-width integers across platforms, prefer the types from `<stdint.h>`:

```c
#include <stdint.h>
int32_t exact32 = 42;   // guaranteed exactly 32 bits
uint64_t exact64 = 100; // guaranteed exactly 64 bits, unsigned
```

---

## 5.5 Type Conversion and Casting

C automatically converts between compatible numeric types in expressions — this is called **implicit conversion**:

```c
int a = 5;
double b = a;  // int implicitly converted to double: 5.0
```

Mixing an `int` and a `double` in division promotes the `int` to `double` before dividing:

```c
int x = 7, y = 2;
double result = x / y;        // 3.0 -- integer division happens FIRST
double correct = (double)x / y;  // 3.5 -- explicit cast forces float division
```

`(double)x` is an **explicit cast** — it tells the compiler exactly how to convert the value, rather than relying on implicit rules. Casts are especially important around integer division, which truncates any fractional part.

---

[Previous](./[4]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[6]-Operators-and-Expressions.md)
