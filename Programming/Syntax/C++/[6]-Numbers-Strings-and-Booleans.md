[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings And Booleans

## 6.1 Integer Types And Overflow

Beyond plain `int`, C++ offers integer types of different sizes and signedness:

```cpp
short s;            // typically 2 bytes
int i;               // typically 4 bytes
long l;              // at least 4 bytes
long long ll;        // at least 8 bytes
unsigned int u;       // no negative values, doubles the positive range
```

Each type has a fixed range. Exceeding it causes **overflow**:

```cpp
#include <climits>
int maxInt = INT_MAX;   // 2147483647 on most systems
int overflowed = maxInt + 1; // undefined behavior for signed overflow
```

For unsigned types, overflow wraps around instead (e.g. `0 - 1` for an `unsigned int` becomes a very large number), which is well-defined but easy to trip over accidentally.

---

## 6.2 Floating-Point Numbers

`float` and `double` store approximate decimal values using a binary format that can't represent every decimal fraction exactly:

```cpp
double a = 0.1 + 0.2;
// a is approximately 0.30000000000000004, not exactly 0.3
```

Because of this, never compare floating-point numbers with `==` directly — compare within a small tolerance instead:

```cpp
#include <cmath>
bool nearlyEqual = std::fabs(a - 0.3) < 1e-9;
```

`double` is the default choice for most floating-point work; `float` is used when memory or performance is tight, such as in graphics.

---

## 6.3 Booleans

`bool` holds exactly two values: `true` or `false`. Internally, `true` is `1` and `false` is `0`, and any non-zero number converts to `true`:

```cpp
bool isOpen = true;
bool hasFailed = false;

if (5) {
    // this runs, because 5 converts to true
}
```

Booleans are most commonly the result of comparisons, covered in the next lesson.

---

## 6.4 Character And String Literals

A `char` holds a single character, written in single quotes. A sequence of characters is a **string literal**, written in double quotes:

```cpp
char letter = 'A';
const char* greeting = "Hello"; // a C-style string (covered later)
```

C++'s richer `std::string` type, from the standard library, is generally preferred over raw `char` arrays for everyday text handling — it's covered in depth in **`std::string` & String Manipulation** later in this course.

---

## 6.5 Escape Sequences

Certain characters can't be typed directly into a literal and need an **escape sequence**, starting with a backslash:

| Sequence | Meaning |
|---|---|
| `\n` | Newline |
| `\t` | Tab |
| `\\` | Backslash |
| `\"` | Double quote |
| `\'` | Single quote |

```cpp
#include <iostream>

int main() {
    std::cout << "Line one\nLine two\tTabbed\n";
    std::cout << "She said \"hello\"\n";
}
```

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[7]-Operators-and-Expressions.md)
