[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators & Expressions

## 7.1 Arithmetic Operators

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

## 7.2 Comparison Operators

Comparison operators compare two values and always return a `bool`:

```python
5 == 5   # True   equal to
5 != 3   # True   not equal to
5 > 3    # True   greater than
5 < 3    # False  less than
5 >= 5   # True   greater than or equal to
5 <= 3   # False  less than or equal to
```

---

## 7.3 Logical Operators

Used to combine or invert boolean expressions:

```python
True and False   # False — both must be True
True or False    # True  — at least one must be True
not True         # False — inverts the value
```

`and`/`or` in Python use **short-circuit evaluation**: they return the actual value that decided the result, not just `True`/`False`, and they may skip evaluating the second operand entirely.

```python
5 and "hello"   # "hello" — first value is truthy, so the second is returned
0 or "default"  # "default" — first value is falsy, so the second is returned
```

---

## 7.4 Assignment Operators

Beyond plain `=`, Python offers augmented assignment operators that combine an operation with assignment:

```python
x = 5
x += 3   # same as x = x + 3  →  8
x -= 2   # x = x - 2  →  6
x *= 4   # x = x * 4  →  24
x /= 3   # x = x / 3  →  8.0
x //= 2  # x = x // 2 →  4.0
x **= 2  # x = x ** 2 →  16.0
```

---

## 7.5 Bitwise Operators

Bitwise operators work on the individual binary bits of integers:

```python
5 & 3    # 1   AND
5 | 3    # 7   OR
5 ^ 3    # 6   XOR
~5       # -6  NOT (inverts all bits)
5 << 1   # 10  left shift
5 >> 1   # 2   right shift
```

These are used far less often in everyday application code but matter for low-level work like flags, masks, and performance-sensitive numeric code.

---

## 7.6 Identity Operators (is, is not)

`is` and `is not` check whether two variables point to the **exact same object in memory**, not just equal values. This is different from `==`, which checks value equality.

```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b   # True  — same contents
a is b   # False — two different list objects in memory

c = a
c is a   # True — c and a refer to the same object
```

A common, correct use of `is` is checking against `None`:

```python
if x is None:
    ...
```

---

## 7.7 Membership Operators (in, not in)

Check whether a value exists within a sequence (string, list, tuple, dict, set):

```python
"a" in "cat"          # True
3 in [1, 2, 3]        # True
5 not in [1, 2, 3]    # True
"key" in {"key": 1}   # True — checks dictionary keys
```

---

## 7.8 Operator Precedence

When an expression mixes multiple operators, Python evaluates them in a fixed order (from highest to lowest priority, simplified):

1. `**` (exponentiation)
2. `+x`, `-x`, `~x` (unary)
3. `*`, `/`, `//`, `%`
4. `+`, `-`
5. Comparisons (`==`, `<`, `in`, `is`, ...)
6. `not`
7. `and`
8. `or`

```python
2 + 3 * 4     # 14, not 20 — multiplication happens first
(2 + 3) * 4   # 20 — parentheses always take priority
```

When in doubt, use parentheses — they cost nothing and make intent explicit.

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[8]-Conditionals.md)
