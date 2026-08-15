[Previous](./[4]-Environment-Variables-and-Configuration.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables & Basic Data Types

## 5.1 What is a Variable?

A variable is a name that refers to a value stored in memory. In Python, you create one simply by assigning a value with `=` — there's no need to declare a type ahead of time.

```python
name = "Ada"
age = 36
```

Here, `name` and `age` are variables pointing to a string and an integer, respectively.

---

## 5.2 Naming Rules & Conventions

Rules (enforced by Python):
- Must start with a letter or underscore, not a digit.
- Can only contain letters, digits, and underscores.
- Case-sensitive (`age` and `Age` are different variables).
- Cannot be a reserved keyword (`class`, `for`, `import`, etc.).

Conventions (style, not enforced, per [PEP 8](https://peps.python.org/pep-0008/)):
- Use `snake_case` for variable names: `first_name`, `total_price`.
- Use descriptive names (`user_count` rather than `uc`).
- Constants are typically written in `ALL_CAPS`: `MAX_RETRIES = 5`.

---

## 5.3 Dynamic Typing

Python is **dynamically typed**: a variable's type is determined by the value currently assigned to it, and can change over the variable's lifetime.

```python
x = 5        # x is an int
x = "five"   # now x is a str — this is legal in Python
```

This is different from statically typed languages (like Java or C), where a variable's type is fixed when it's declared.

---

## 5.4 Basic Data Types Overview

Python's built-in core types include:

| Type | Example | Description |
|---|---|---|
| `int` | `42` | Whole numbers |
| `float` | `3.14` | Decimal numbers |
| `str` | `"hello"` | Text |
| `bool` | `True` / `False` | Boolean values |
| `NoneType` | `None` | Represents "no value" |

Numbers, strings, and booleans are covered in depth in the next lesson.

---

## 5.5 Type Checking with type() and isinstance()

```python
x = 42
print(type(x))            # <class 'int'>
print(isinstance(x, int)) # True
print(isinstance(x, (int, float)))  # True — checks against multiple types
```

`isinstance()` is generally preferred over comparing `type(x) == int` directly, because it also correctly handles subclasses.

---

## 5.6 Type Casting/Conversion

You can explicitly convert between types using built-in functions:

```python
int("42")      # 42
str(42)        # "42"
float("3.14")  # 3.14
int(3.9)       # 3  (truncates, doesn't round)
bool(0)        # False
bool(1)        # True
```

Conversions that don't make sense raise an error:

```python
int("hello")  # ValueError: invalid literal for int() with base 10: 'hello'
```

[Previous](./[4]-Environment-Variables-and-Configuration.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)
