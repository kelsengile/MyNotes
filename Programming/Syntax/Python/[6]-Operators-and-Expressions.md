[Previous](./[5]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[7]-Conditionals.md)

# Lesson 6 - Operators & Expressions

---

## 6.1 Arithmetic Operators



```python
7 + 3    # 10  addition
7 - 3    # 4   subtraction
7 * 3    # 21  multiplication
7 / 3    # 2.333... true division, always returns a float
7 // 3   # 2   floor division, rounds down to an int
7 % 3    # 1   modulo, the remainder
7 ** 3   # 343 exponentiation
```

Note that `/` always produces a `float`, even when the numbers divide evenly (`8 / 2` is `4.0`, not `4`). Use `//` when you specifically need an integer result.

---

## 6.2 Comparison Operators



```python
5 == 5    # True  equal to
5 != 3    # True  not equal to
5 > 3     # True
5 < 3     # False
5 >= 5    # True
5 <= 4    # False
```

These return a `bool` and can be chained: `1 < x < 10` checks both conditions at once.

---

## 6.3 Logical Operators



```python
True and False   # False — both must be True
True or False    # True  — at least one must be True
not True         # False — inverts the value
```

`and` / `or` are short-circuiting: they stop evaluating as soon as the result is determined, and return one of the original operands rather than a plain `True`/`False`:

```python
0 or "default"   # "default"
"" and "default" # "" 
```

---

## 6.4 Bitwise Operators


Operate on the binary representation of integers:

```python
5 & 3    # 1   AND
5 | 3    # 7   OR
5 ^ 3    # 6   XOR
~5       # -6  NOT
5 << 1   # 10  left shift
5 >> 1   # 2   right shift
```

These are used far less often than arithmetic or logical operators — mainly in low-level flag manipulation, performance-critical code, or working with binary data.

---

## 6.5 Identity and Membership Operators


```python
a is b        # True if a and b are the SAME object in memory
a is not b    # True if they are different objects
"a" in "cat"  # True — membership: is "a" found in "cat"?
"z" not in "cat"  # True
```

`is` checks object identity, not equality — `a == b` can be `True` while `a is b` is `False` (two separate lists with the same contents, for example). Use `==` to compare values and `is` almost exclusively for comparing to `None`: `if x is None:`.

---

[Previous](./[5]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[7]-Conditionals.md)
