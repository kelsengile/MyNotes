[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[5]-Numbers-and-Characters.md)

*Core Syntax*

# Lesson 4 - Variables And Data Types

## 4.1 What Is a Variable?

A variable is a named location in memory that holds a value. In C, every variable has a fixed **type**, decided when it's declared, which determines what kind of data it can store and how much memory it occupies.

```c
int age;       // declares a variable named 'age' of type int
age = 25;      // assigns a value to it
```

Unlike dynamically-typed languages, C requires you to know — and state — a variable's type up front. This is part of what lets C run so efficiently: the compiler knows exactly how much memory each variable needs before the program ever runs.

---

## 4.2 Declaring and Initializing Variables

You can declare a variable and assign it a value in one step:

```c
int score = 100;
float price = 19.99f;
char grade = 'A';
```

Multiple variables of the same type can be declared together:

```c
int x = 0, y = 0, z = 0;
```

An uninitialized local variable contains **garbage** — whatever bits happened to already be in that memory — not a predictable default like `0`. Always initialize variables before reading them.

```c
int total;
printf("%d\n", total);  // undefined behavior: reading garbage memory
```

---

## 4.3 Basic Data Types Overview

C's core built-in types:

| Type | Stores | Example |
|---|---|---|
| `int` | Whole numbers | `int count = 42;` |
| `float` | Single-precision decimals | `float pi = 3.14f;` |
| `double` | Double-precision decimals | `double e = 2.718281828;` |
| `char` | A single character | `char letter = 'x';` |
| `_Bool` (or `bool` with `<stdbool.h>`) | true/false | `bool done = false;` |

This lesson covers the basics; Lesson 5 goes deeper into integer signedness, floating-point precision, and exact type sizes.

---

## 4.4 Constants

Use `const` to declare a variable whose value cannot change after initialization:

```c
const double GRAVITY = 9.81;
GRAVITY = 10.0;  // compile error: assignment of read-only variable
```

For values known entirely at compile time, a preprocessor macro is a common alternative (covered fully in Lesson 10):

```c
#define MAX_USERS 100
```

Prefer `const` over `#define` when you want the compiler to type-check the value — macros are simple text substitution with no type information.

---

## 4.5 Naming Conventions

C doesn't enforce a naming style, but conventions make code far easier to read:

- Variable and function names: `lower_snake_case` (e.g. `user_count`, `calculate_total`).
- Constants and macros: `ALL_CAPS_SNAKE_CASE` (e.g. `MAX_BUFFER_SIZE`).
- Names must start with a letter or underscore, and may contain letters, digits, and underscores — never spaces or hyphens.
- Avoid names starting with underscores followed by a capital letter (`_Foo`) or double underscores (`__foo`) — these are reserved for the compiler and standard library.

Choose descriptive names. `int d;` tells a reader nothing; `int days_elapsed;` does.

---

[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[5]-Numbers-and-Characters.md)
