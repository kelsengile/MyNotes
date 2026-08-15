[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals: if, elif, else

## 8.1 The if Statement

An `if` statement runs a block of code only when its condition evaluates to `True`. Python uses **indentation** (not braces) to define which lines belong to the block — the standard is 4 spaces:

```python
age = 20

if age >= 18:
    print("You are an adult.")
```

---

## 8.2 elif and else

`elif` (short for "else if") lets you check additional conditions if the first one is `False`. `else` provides a fallback that runs when none of the conditions matched:

```python
score = 75

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"

print(grade)  # "C"
```

Python checks conditions top to bottom and stops at the first one that is `True` — later `elif` branches are skipped even if they'd also be `True`.

---

## 8.3 Nested Conditionals

`if` statements can be placed inside other `if` blocks to check more specific conditions:

```python
if age >= 18:
    if has_id:
        print("Entry allowed.")
    else:
        print("Need ID.")
else:
    print("Too young.")
```

Deeply nested conditionals can get hard to read — combining conditions with `and`/`or` is often clearer:

```python
if age >= 18 and has_id:
    print("Entry allowed.")
```

---

## 8.4 Conditional (Ternary) Expressions

For simple cases, Python offers a one-line conditional expression:

```python
status = "adult" if age >= 18 else "minor"
```

This is equivalent to a full `if`/`else` block but is best kept short and simple — for anything complex, a regular `if` statement is more readable.

---

## 8.5 Truthy and Falsy Values in Conditions

Because every value has an inherent truthiness (see Lesson 2), you can use non-boolean values directly in a condition:

```python
name = ""

if name:
    print(f"Hello, {name}")
else:
    print("No name provided")   # this runs — empty string is falsy
```

This is idiomatic Python, but be careful: `if x:` treats `0`, `None`, and `False` identically. If you specifically need to distinguish "the value is `None`" from "the value is `0`," check explicitly with `if x is None:`.

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[9]-Loops.md)
