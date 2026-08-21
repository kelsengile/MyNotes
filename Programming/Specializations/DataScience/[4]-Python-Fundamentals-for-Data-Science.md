*Core Programming for Data Science*

# Lesson 4 - Python Fundamentals for Data Science

[Previous](./[3]-Anatomy-of-a-Data-Science-Project.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[5]-Working-with-NumPy-Arrays.md)

---

## 4.1 Core Data Types

Python's built-in types you'll use constantly in data work:

```python
x = 5             # int
y = 3.14          # float
name = "Ada"      # str
is_valid = True   # bool
values = [1, 2, 3]          # list — ordered, mutable
point = (10, 20)             # tuple — ordered, immutable
person = {"name": "Ada", "age": 30}  # dict — key/value pairs
unique_ids = {1, 2, 3}       # set — unordered, no duplicates
```

Lists and dictionaries are the two you'll reach for most often when handling data before it becomes a proper table.

---

## 4.2 Control Flow

```python
# Conditionals
if age >= 18:
    status = "adult"
elif age >= 13:
    status = "teen"
else:
    status = "child"

# Loops
for value in values:
    print(value)

# List comprehensions — a compact way to build lists
squares = [v ** 2 for v in values]
evens = [v for v in values if v % 2 == 0]
```

List comprehensions are used heavily in data science code because they're concise and fast.

---

## 4.3 Functions

```python
def normalize(value, min_val, max_val):
    """Scale a value to the 0-1 range."""
    return (value - min_val) / (max_val - min_val)

result = normalize(75, 0, 100)  # 0.75
```

Writing small, well-named functions makes data cleaning and analysis code far easier to test and reuse than one long script.

---

## 4.4 Working with Files and Modules

```python
# Reading a plain text file
with open("data.txt") as f:
    contents = f.read()

# Importing libraries (covered in the next lessons)
import numpy as np
import pandas as pd
```

The `with` statement ensures files are properly closed even if an error occurs. `import ... as` gives a library a short alias — `np` and `pd` are near-universal conventions you'll see throughout the data science world.

---

[Previous](./[3]-Anatomy-of-a-Data-Science-Project.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[5]-Working-with-NumPy-Arrays.md)
