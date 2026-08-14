[Previous](./[7]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[9]-Functions-and-Scope.md)

---

Lesson 8 - Loops

## 8.1 The for Loop


Python's `for` loop iterates over the items of a sequence (list, string, range, etc.), rather than counting with an index like in some other languages:

```python
for fruit in ["apple", "banana", "cherry"]:
    print(fruit)

for i in range(5):        # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 10, 2): # 2, 4, 6, 8 — start, stop, step
    print(i)
```

`range()` generates numbers lazily and is the standard way to loop a fixed number of times.

---

## 8.2 The while Loop


`while` repeats a block as long as a condition remains true:

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

Use `while` when you don't know in advance how many iterations you'll need (e.g., waiting for user input, polling a condition) — use `for` when iterating over a known collection or range.

---

## 8.3 break, continue, and pass


```python
for n in range(10):
    if n == 5:
        break       # exit the loop entirely
    if n % 2 == 0:
        continue    # skip to the next iteration
    print(n)

if some_condition:
    pass  # a no-op placeholder, useful for stub code
```

`pass` does nothing and is commonly used when Python's syntax requires a block but you have no code to put there yet (e.g., an empty function body while stubbing out design).

---

## 8.4 The else Clause on Loops


Both `for` and `while` support an optional `else` block, which runs only if the loop completes **without** hitting a `break`:

```python
for n in range(2, 10):
    if n % 7 == 0:
        print("Found a multiple of 7")
        break
else:
    print("No multiple of 7 found")
```

This is a Python-specific feature not found in most languages — it's most useful for search loops where you want to know whether a `break` happened.

---

[Previous](./[7]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[9]-Functions-and-Scope.md)
