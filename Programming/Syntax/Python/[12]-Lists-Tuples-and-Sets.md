[Previous](./[11]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[13]-Dictionaries.md)


# Lesson 12 - Lists, Tuples & Sets

---

## 12.1 Lists


A `list` is an ordered, **mutable** collection that allows duplicates:

```python
nums = [3, 1, 4, 1, 5]
nums.append(9)        # [3, 1, 4, 1, 5, 9]
nums.insert(0, 0)      # [0, 3, 1, 4, 1, 5, 9]
nums.remove(1)          # removes the FIRST 1 found
nums.pop()               # removes and returns the last item
nums.sort()               # sorts in place
len(nums)                  # number of items
```

Because lists are mutable, assigning `b = a` makes `b` a reference to the *same* list, not a copy:

```python
a = [1, 2, 3]
b = a
b.append(4)
print(a)   # [1, 2, 3, 4] — a changed too!
```

Use `b = a.copy()` or `b = a[:]` to make an independent copy.

---

## 12.2 Tuples


A `tuple` is an ordered, **immutable** collection — once created, it cannot be changed:

```python
point = (3, 4)
x, y = point          # unpacking
point[0]                # 3
# point[0] = 5         # TypeError: tuples don't support item assignment
```

Because they're immutable, tuples are useful for fixed collections of related values (like coordinates), and they can be used as dictionary keys or set members, unlike lists.

---

## 12.3 Sets


A `set` is an unordered collection of **unique** items, with no duplicates and no guaranteed order:

```python
s = {1, 2, 3, 2, 1}    # {1, 2, 3} — duplicates automatically dropped
s.add(4)
s.remove(1)

a = {1, 2, 3}
b = {2, 3, 4}
a | b   # union:        {1, 2, 3, 4}
a & b   # intersection: {2, 3}
a - b   # difference:   {1}
```

Sets are ideal for membership tests (`x in my_set` is very fast) and for removing duplicates from a collection: `unique = set(my_list)`.

---

## 12.4 Choosing the Right Structure


| Need | Use |
|---|---|
| Ordered, changeable collection, duplicates OK | `list` |
| Ordered, fixed collection that shouldn't change | `tuple` |
| Unordered, unique items, fast membership checks | `set` |
| Key-value lookups | `dict` (Lesson 13) |

Choosing the right structure up front makes code both clearer and more efficient — e.g., checking membership in a `set` is dramatically faster than in a `list` for large collections.

---

[Previous](./[11]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[13]-Dictionaries.md)
