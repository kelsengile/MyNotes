[Previous](./[3]-Virtual-Environments-and-Pip.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[5]-Numbers-Strings-and-Booleans.md)

# Lesson 4 - Variables & Basic Data Types

---

## 4.1 What is a Variable?



A variable is a name bound to a value in memory. Python creates a variable the moment you assign to it — there's no separate declaration step.

```python
age = 25
name = "Ada"
```

Assignment (`=`) always creates or updates a binding; it never copies data implicitly for mutable objects, a distinction covered further in Lesson 12.

---

## 4.2 Naming Rules and Conventions



Rules (enforced by Python):
- Must start with a letter or underscore, followed by letters, digits, or underscores.
- Case-sensitive (`age` and `Age` are different variables).
- Cannot be a reserved keyword (`if`, `class`, `for`, etc.).

Conventions (not enforced, but expected — see PEP 8, covered in Lesson 83):
- Use `snake_case` for variable and function names: `first_name`, `total_score`.
- Use `UPPER_SNAKE_CASE` for constants: `MAX_RETRIES = 3`.
- Choose descriptive names over short, cryptic ones.

---

## 4.3 Dynamic Typing



Python is **dynamically typed**: a variable's type is determined by the value it currently holds, and can change.

```python
x = 5        # x is an int
x = "five"   # now x is a str — this is legal
```

This differs from statically typed languages where a variable's type is fixed at declaration. Dynamic typing gives flexibility but shifts responsibility for catching type-related bugs to testing (or optional type hints, covered in Lesson 27).

---

## 4.4 Basic Data Types Overview


Python's built-in types you'll use constantly:

| Type | Example | Description |
|---|---|---|
| `int` | `42` | Whole numbers |
| `float` | `3.14` | Decimal numbers |
| `str` | `"hello"` | Text |
| `bool` | `True` / `False` | Logical values |
| `list` | `[1, 2, 3]` | Ordered, mutable collection |
| `tuple` | `(1, 2, 3)` | Ordered, immutable collection |
| `dict` | `{"a": 1}` | Key-value mapping |
| `set` | `{1, 2, 3}` | Unordered, unique collection |
| `NoneType` | `None` | Represents "no value" |

Check any value's type with the built-in `type()` function:

```python
type(42)        # <class 'int'>
type("hello")   # <class 'str'>
```

Numbers, strings, and booleans are covered in depth in the next lesson; the collection types get full lessons of their own later in this Topic.

---

[Previous](./[3]-Virtual-Environments-and-Pip.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[5]-Numbers-Strings-and-Booleans.md)
