[Previous](./[4]-CPP-Standards.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables And Data Types

## 5.1 Declaring Variables

A variable is a named piece of memory that stores a value. In C++, you declare a variable by writing its **type**, then its **name**:

```cpp
int age;        // declaration, no value yet
age = 25;       // assignment

int score = 100; // declaration + initialization in one step
```

C++ is **statically typed**: once a variable's type is set, it can't change. This lets the compiler catch type errors before the program ever runs.

---

## 5.2 Fundamental Data Types

C++ provides several built-in ("fundamental") types:

| Type | Holds | Example |
|---|---|---|
| `int` | Whole numbers | `int count = 42;` |
| `double` | Decimal numbers | `double pi = 3.14159;` |
| `float` | Decimal numbers (lower precision) | `float ratio = 0.5f;` |
| `char` | A single character | `char grade = 'A';` |
| `bool` | `true` or `false` | `bool isReady = true;` |

Each type reserves a specific amount of memory and determines what operations make sense on it.

---

## 5.3 Type Sizes And sizeof

Type sizes can vary by platform, but common sizes on most modern systems are:

```cpp
sizeof(int);    // usually 4 bytes
sizeof(double); // usually 8 bytes
sizeof(char);   // always 1 byte
sizeof(bool);   // usually 1 byte
```

`sizeof` is a compile-time operator that reports how many bytes a type (or variable) occupies:

```cpp
#include <iostream>

int main() {
    std::cout << sizeof(int) << "\n"; // e.g. 4
}
```

---

## 5.4 Constants (const, constexpr)

A variable marked `const` cannot be changed after initialization:

```cpp
const double taxRate = 0.08;
// taxRate = 0.10; // compile error
```

`constexpr` goes further, requiring the value to be known **at compile time**, which allows the compiler to optimize more aggressively:

```cpp
constexpr int maxUsers = 100;
```

Use `const` for values that shouldn't change after being set, and `constexpr` when the value is known ahead of time and used in contexts requiring compile-time constants, like array sizes.

---

## 5.5 Type Conversion Basics

C++ converts between types **implicitly** in some situations, and lets you convert **explicitly** in others.

```cpp
int wholeNumber = 5;
double decimal = wholeNumber; // implicit: int -> double, no data lost

double price = 9.99;
int rounded = static_cast<int>(price); // explicit: double -> int, decimal is truncated to 9
```

Prefer `static_cast<T>(value)` for explicit conversions — it's clearer and safer than older C-style casts like `(int)price`.

[Previous](./[4]-CPP-Standards.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)
