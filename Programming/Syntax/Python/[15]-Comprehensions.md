[Previous](./[14]-Dictionaries.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[16]-Collections-Module.md)

*Data Structures*

# Lesson 15 - Comprehensions: List, Dict & Set

## 15.1 List Comprehensions

A **list comprehension** builds a new list from an existing iterable in a single, readable line, replacing the more verbose loop-and-append pattern:

```python
# Traditional loop
squares = []
for n in range(10):
    squares.append(n ** 2)

# Equivalent list comprehension
squares = [n ** 2 for n in range(10)]
```

The general shape is `[expression for item in iterable]`.

---

## 15.2 Conditional Logic in Comprehensions

You can filter items with an `if` clause, and/or branch the expression itself with an inline `if/else`:

```python
evens = [n for n in range(20) if n % 2 == 0]          # filter (if AFTER the for)

labels = ["even" if n % 2 == 0 else "odd" for n in range(5)]  # if/else BEFORE the for
```

Note the difference in placement: a filtering `if` goes at the end; a value-choosing `if/else` goes at the beginning, right after the expression.

---

## 15.3 Dict Comprehensions

Build a dictionary in one line using `{key_expr: value_expr for item in iterable}`:

```python
names = ["Ada", "Bo", "Cy"]
name_lengths = {name: len(name) for name in names}
# {"Ada": 3, "Bo": 2, "Cy": 2}

squares = {n: n ** 2 for n in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

---

## 15.4 Set Comprehensions

Nearly identical to list comprehensions, but with curly braces, producing a set of unique values:

```python
words = ["apple", "banana", "cherry", "apple"]
lengths = {len(word) for word in words}   # {5, 6}  — duplicates removed automatically
```

---

## 15.5 Nested Comprehensions

Comprehensions can be nested to work with nested data, such as flattening a 2D list:

```python
matrix = [[1, 2, 3], [4, 5, 6]]

flattened = [num for row in matrix for num in row]
# [1, 2, 3, 4, 5, 6]

# equivalent nested loop for comparison:
flattened = []
for row in matrix:
    for num in row:
        flattened.append(num)
```

---

## 15.6 When Not to Use Comprehensions

Comprehensions are best for simple, single-expression transformations. Avoid them when:

- The logic requires multiple statements or complex branching — a regular loop is clearer.
- The comprehension would need to be nested more than one or two levels deep — readability suffers fast.
- You don't actually need the resulting collection (e.g. you're just looping for side effects like printing) — use a plain `for` loop instead.

```python
# Hard to read — prefer a normal loop instead
result = [x for sub in data if isinstance(sub, list) for x in sub if x > 0 and x % 2 == 0]
```

[Previous](./[14]-Dictionaries.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[16]-Collections-Module.md)
