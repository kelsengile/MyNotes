[Previous](./[13]-Dictionaries.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[15]-Collections-Module.md)


# Lesson 14 - Comprehensions

--- 
## 14.1 List Comprehensions


A list comprehension builds a new list from an existing iterable in a single, readable expression:

```python
squares = [x ** 2 for x in range(10)]
# equivalent to:
squares = []
for x in range(10):
    squares.append(x ** 2)
```

The comprehension form is generally preferred for simple transformations — it's shorter and, in most cases, faster than the equivalent `for` loop.


## 14.2 Dict Comprehensions

---

The same pattern works for dictionaries, producing key-value pairs:

```python
words = ["apple", "kiwi", "banana"]
lengths = {word: len(word) for word in words}
# {'apple': 5, 'kiwi': 4, 'banana': 6}
```

---

## 14.3 Set Comprehensions


And for sets, using curly braces without a colon:

```python
nums = [1, 2, 2, 3, 3, 3]
unique_squares = {n ** 2 for n in nums}
# {1, 4, 9}
```

---

## 14.4 Nested and Conditional Comprehensions


Comprehensions can include a filtering condition (`if`) and can be nested for multi-dimensional data:

```python
# Filtering: only even squares
evens = [x ** 2 for x in range(10) if x % 2 == 0]

# Conditional expression inside the comprehension (if/else, not filtering)
labels = ["even" if x % 2 == 0 else "odd" for x in range(5)]

# Nested: flatten a list of lists
matrix = [[1, 2], [3, 4], [5, 6]]
flat = [num for row in matrix for num in row]
# [1, 2, 3, 4, 5, 6]
```

Comprehensions are powerful, but readability matters — if a comprehension needs more than one `if` or nested loop to understand at a glance, a regular `for` loop is often clearer.

---

[Previous](./[13]-Dictionaries.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[15]-Collections-Module.md)
