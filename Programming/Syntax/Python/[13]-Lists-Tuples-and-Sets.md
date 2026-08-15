[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[14]-Dictionaries.md)

*Data Structures*

# Lesson 13 - Lists, Tuples & Sets

## 13.1 Lists: Ordered & Mutable

A **list** is an ordered, mutable (changeable) collection, created with square brackets:

```python
fruits = ["apple", "banana", "cherry"]

fruits[0]        # "apple" — indexing
fruits[-1]       # "cherry" — last item
fruits[0:2]      # ["apple", "banana"] — slicing
fruits[1] = "blueberry"   # mutable — items can be reassigned
len(fruits)      # 3
```

Lists can hold mixed types and can be nested inside one another.

---

## 13.2 Common List Methods

```python
fruits = ["apple", "banana"]

fruits.append("cherry")       # add to the end → ["apple", "banana", "cherry"]
fruits.insert(1, "kiwi")      # insert at index 1
fruits.remove("banana")       # remove first matching value
fruits.pop()                  # remove & return the last item
fruits.pop(0)                 # remove & return item at index 0
fruits.sort()                 # sort in place
fruits.reverse()              # reverse in place
sorted(fruits)                # returns a NEW sorted list, original unchanged
fruits.count("apple")         # count occurrences
fruits.index("apple")         # find index of first match
fruits.extend(["mango"])      # append all items from another iterable
```

---

## 13.3 Tuples: Ordered & Immutable

A **tuple** looks like a list but is created with parentheses and is **immutable** — once created, it cannot be changed:

```python
point = (3, 4)
point[0]         # 3 — indexing works, same as lists
point[0] = 10     # TypeError: 'tuple' object does not support item assignment

single = (5,)     # a single-item tuple needs a trailing comma
```

---

## 13.4 Why Use Tuples?

- **Data integrity** — a tuple guarantees its contents won't accidentally change later in the program.
- **Hashability** — tuples (of hashable items) can be used as dictionary keys or set members, unlike lists.
- **Multiple return values** — functions commonly return a tuple to hand back several values at once:

```python
def min_max(numbers):
    return min(numbers), max(numbers)   # returns a tuple

low, high = min_max([3, 1, 4, 1, 5])    # unpacking
```

- **Slight performance edge** — tuples are generally faster to create and iterate over than lists.

---

## 13.5 Sets: Unordered & Unique

A **set** is an unordered collection of unique, hashable items, created with curly braces or `set()`:

```python
numbers = {1, 2, 3, 3, 2}
numbers            # {1, 2, 3} — duplicates are automatically removed

s = set()           # empty set — note: {} creates an empty DICT, not a set
s.add(4)
s.remove(4)
3 in numbers        # True — membership checks are very fast on sets
```

Sets have no indexing or defined order, since they aren't sequences.

---

## 13.6 Set Operations

Sets support classic mathematical set operations:

```python
a = {1, 2, 3}
b = {2, 3, 4}

a | b   # {1, 2, 3, 4}   union
a & b   # {2, 3}          intersection
a - b   # {1}             difference (in a, not in b)
a ^ b   # {1, 4}          symmetric difference (in one but not both)

a.issubset(b)      # False
a.issuperset({1})  # True
```

These are especially useful for quickly comparing two collections or removing duplicates from a list: `list(set(my_list))`.

[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[14]-Dictionaries.md)
