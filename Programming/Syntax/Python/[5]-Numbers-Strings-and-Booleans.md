[Previous](./[4]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[6]-Operators-and-Expressions.md)

# Lesson 5 - Numbers, Strings & Booleans

---

## 5.1 Numeric Types: int, float, complex



```python
whole = 10          # int — arbitrary precision, no overflow
decimal = 3.14       # float — 64-bit double precision
imaginary = 2 + 3j   # complex — rarely needed outside math/engineering
```

`int` values in Python can grow to any size limited only by available memory. `float` values follow the IEEE 754 standard, which means some decimals can't be represented exactly:

```python
0.1 + 0.2   # 0.30000000000000004
```

This is a floating-point precision quirk shared by nearly all programming languages, not a Python bug — avoid comparing floats with `==` directly when precision matters.

---

## 5.2 Strings



Strings (`str`) are immutable sequences of characters, written with single, double, or triple quotes:

```python
a = 'single quotes'
b = "double quotes"
c = '''triple quotes
can span multiple lines'''
```

Being immutable means operations like concatenation build a *new* string rather than modifying the original:

```python
greeting = "Hello"
greeting += ", world"   # creates a new string, reassigns the name
```

---

## 5.3 Booleans and Truthiness



`bool` has exactly two values, `True` and `False`, and is technically a subtype of `int` (`True == 1`, `False == 0`).

Every Python object has an inherent truth value when evaluated in a boolean context (like an `if`). These are considered "falsy":

```python
False, None, 0, 0.0, "", [], (), {}, set()
```

Everything else is "truthy." This lets you write `if my_list:` instead of `if len(my_list) > 0:`.

---

## 5.4 Type Conversion



Convert between types explicitly with built-in functions:

```python
int("42")      # 42
float("3.14")  # 3.14
str(42)        # "42"
bool(0)        # False
bool("no")     # True — any non-empty string is truthy
```

Conversions that don't make sense raise a `ValueError`:

```python
int("hello")   # ValueError: invalid literal for int() with base 10: 'hello'
```

Exception handling for cases like this is covered in Lesson 11.

---

[Previous](./[4]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[6]-Operators-and-Expressions.md)
