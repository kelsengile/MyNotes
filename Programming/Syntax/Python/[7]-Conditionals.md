[Previous](./[6]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[8]-Loops.md)


#Lesson 7 - Conditionals

---

## 7.1 The if Statement


```python
age = 20
if age >= 18:
    print("You are an adult")
```

Python uses **indentation** (not braces) to define blocks. The standard convention is 4 spaces per indentation level. Mixing tabs and spaces will raise an error.

---

## 7.2 elif and else


```python
score = 72
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

Python checks conditions top to bottom and runs only the first branch that matches. `elif` (short for "else if") lets you chain multiple conditions without nesting; `else` is optional and catches everything else.

---

## 7.3 Nested and Chained Conditionals


Conditionals can be nested inside one another:

```python
if age >= 18:
    if has_id:
        print("Entry allowed")
    else:
        print("ID required")
else:
    print("Too young")
```

Deep nesting hurts readability — often it can be flattened using chained comparisons or combined boolean logic:

```python
if 18 <= age < 65:
    print("Standard rate")
```

---

## 7.4 Conditional (Ternary) Expressions


For simple either/or assignments, Python offers a one-line conditional expression:

```python
status = "adult" if age >= 18 else "minor"
```

This reads as: evaluate `"adult"` if the condition is true, otherwise evaluate `"minor"`. Use it for short, simple choices — for anything with multiple branches or side effects, a full `if`/`elif`/`else` block is clearer.

---

[Previous](./[6]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[8]-Loops.md)
