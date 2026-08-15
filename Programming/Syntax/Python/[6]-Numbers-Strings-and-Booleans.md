[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings & Booleans

## 6.1 Integers and Floats

Python has two primary numeric types for everyday use:

```python
whole = 10          # int — arbitrary precision, no fixed size limit
decimal = 10.5       # float — a 64-bit double-precision number
```

Python's `int` has **arbitrary precision**, meaning it can represent numbers as large as your computer's memory allows, without overflow. Floats, like in most languages, have limited precision and can suffer from small rounding errors (e.g. `0.1 + 0.2` gives `0.30000000000000004`).

---

## 6.2 Arithmetic with Numbers

```python
7 + 3    # 10  addition
7 - 3    # 4   subtraction
7 * 3    # 21  multiplication
7 / 3    # 2.3333...  true division — always returns a float
7 // 3   # 2   floor division — rounds down to the nearest whole number
7 % 3    # 1   modulo — the remainder
7 ** 3   # 343 exponentiation
```

---

## 6.3 Strings: Creation and Basics

A string is a sequence of characters, created with single, double, or triple quotes:

```python
a = 'hello'
b = "hello"
c = '''a
multi-line
string'''
```

Triple-quoted strings can span multiple lines and are also used for docstrings (documentation comments). Strings support concatenation and repetition:

```python
"Py" + "thon"   # "Python"
"ab" * 3        # "ababab"
len("hello")    # 5
```

---

## 6.4 String Immutability

Strings in Python are **immutable** — once created, a string's contents can never be changed in place. Any operation that appears to "modify" a string actually creates a brand-new string.

```python
s = "hello"
s[0] = "H"   # TypeError: 'str' object does not support item assignment

s = "H" + s[1:]   # this creates a new string instead
```

---

## 6.5 Booleans and Truthiness

The `bool` type has exactly two values: `True` and `False`. Booleans are the result of comparisons and logical operations, and are actually a subclass of `int` (`True == 1`, `False == 0`).

Every Python value has an inherent **truthiness** when evaluated in a boolean context (like an `if` statement). These values are considered "falsy":

```python
False, None, 0, 0.0, "", [], (), {}, set()
```

Everything else is "truthy":

```python
if "hello":   # truthy — this block runs
    print("non-empty strings are truthy")

if []:        # falsy — this block does NOT run
    print("never printed")
```

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[7]-Operators-and-Expressions.md)
