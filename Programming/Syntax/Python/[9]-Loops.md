[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[10]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops: for & while, break, continue, pass

## 9.1 The for Loop

A `for` loop iterates over the items of a sequence (list, tuple, string, dictionary, etc.) one at a time:

```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)
```

Unlike `for` loops in some other languages, Python's `for` always iterates over an **iterable** — there's no manual index/counter management required.

---

## 9.2 The range() Function

When you need to loop a specific number of times, `range()` generates a sequence of numbers:

```python
for i in range(5):        # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 10, 2):  # start=2, stop=10 (exclusive), step=2 → 2, 4, 6, 8
    print(i)
```

To loop with both the index and value of a list, use `enumerate()` instead of manually tracking an index:

```python
for index, fruit in enumerate(fruits):
    print(index, fruit)
```

---

## 9.3 The while Loop

A `while` loop repeats as long as its condition remains `True`. It's used when you don't know in advance how many iterations you'll need:

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

Be careful: if the condition never becomes `False`, you get an **infinite loop**. Always make sure something inside the loop eventually changes the condition.

---

## 9.4 break, continue and pass

- **`break`** exits the loop immediately, skipping any remaining iterations.
- **`continue`** skips the rest of the current iteration and moves to the next one.
- **`pass`** does nothing — it's a placeholder used where Python syntactically requires a statement but you have no code to write yet.

```python
for i in range(10):
    if i == 5:
        break        # stop the loop entirely once i is 5
    if i % 2 == 0:
        continue      # skip even numbers, go to next iteration
    print(i)          # prints 1, 3

for i in range(3):
    pass  # TODO: implement this later
```

---

## 9.5 The else Clause on Loops

Both `for` and `while` loops support an optional `else` block, which runs only if the loop completes **without** hitting a `break`:

```python
for n in range(2, 10):
    for factor in range(2, n):
        if n % factor == 0:
            print(f"{n} is not prime")
            break
    else:
        print(f"{n} is prime")
```

This is a lesser-known Python feature, useful for "search and report if nothing was found" patterns.

---

## 9.6 Nested Loops

Loops can be placed inside other loops, commonly used for working with grids or combinations of items:

```python
for row in range(3):
    for col in range(3):
        print(f"({row}, {col})", end=" ")
    print()
```

A `break` or `continue` inside an inner loop only affects that inner loop, not the outer one.

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[10]-Functions-and-Scope.md)
