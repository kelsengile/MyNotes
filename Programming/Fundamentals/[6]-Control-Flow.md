[Previous](./[5]-Core-Syntax-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[7]-Data-Structures.md)

# Lesson 6 - Control Flow

Control flow statements determine the order in which a program's instructions are executed. Instead of running line by line from top to bottom, control flow lets a program make decisions, repeat actions, and skip or exit sections of code based on conditions.

---

## 6.1 Conditional Statements (if/else, switch/match)

Conditional statements let a program choose between different paths of execution depending on whether an expression evaluates to true or false.

### 6.1.1 The `if` Statement

The simplest conditional runs a block of code only when a condition is true.

```python
# Python
age = 20
if age >= 18:
    print("You are an adult.")
```

```javascript
// JavaScript
let age = 20;
if (age >= 18) {
    console.log("You are an adult.");
}
```

```c
// C
int age = 20;
if (age >= 18) {
    printf("You are an adult.\n");
}
```

### 6.1.2 `if` / `else`

`else` provides an alternative block that runs when the condition is false.

```python
if age >= 18:
    print("Adult")
else:
    print("Minor")
```

### 6.1.3 `else if` / `elif` Chains

Multiple conditions can be checked in sequence. The first true condition's block executes, and the rest are skipped.

```python
score = 82
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

```javascript
let score = 82;
let grade;
if (score >= 90) {
    grade = "A";
} else if (score >= 80) {
    grade = "B";
} else if (score >= 70) {
    grade = "C";
} else {
    grade = "F";
}
```

**Key points:**
- Conditions are evaluated top to bottom; only the first matching branch runs.
- An `else` block is optional and catches every case not handled above it.
- Deeply nested `if/else` chains can often be simplified with early returns, `switch`/`match`, or lookup tables.

### 6.1.4 Ternary / Conditional Expressions

A shorthand for simple if/else assignments.

```javascript
// JavaScript
let status = age >= 18 ? "adult" : "minor";
```

```python
# Python
status = "adult" if age >= 18 else "minor"
```

### 6.1.5 `switch` Statements

A `switch` compares one value against multiple possible cases, which is often cleaner than a long `else if` chain when checking a single variable against discrete values.

```javascript
// JavaScript
switch (day) {
    case "Mon":
    case "Tue":
    case "Wed":
    case "Thu":
    case "Fri":
        console.log("Weekday");
        break;
    case "Sat":
    case "Sun":
        console.log("Weekend");
        break;
    default:
        console.log("Invalid day");
}
```

```c
// C
switch (day) {
    case 1:
        printf("Monday\n");
        break;
    case 2:
        printf("Tuesday\n");
        break;
    default:
        printf("Unknown\n");
}
```

**Important behavior — fall-through:** In C-family languages (C, C++, Java, JavaScript), execution continues into the next case unless a `break` is used. This can be intentional (grouping cases, as above) or a common source of bugs when forgotten.

### 6.1.6 `match` Statements (Pattern Matching)

Modern languages like Python (3.10+), Rust, and Swift offer `match`, which is more powerful than a traditional `switch` — it can match on structure, types, ranges, and destructure values, and does not fall through by default.

```python
# Python 3.10+
match command:
    case "start":
        print("Starting...")
    case "stop":
        print("Stopping...")
    case ("move", direction):
        print(f"Moving {direction}")
    case _:
        print("Unknown command")
```

```rust
// Rust
match number {
    1 => println!("one"),
    2 | 3 => println!("two or three"),
    4..=10 => println!("four through ten"),
    _ => println!("something else"),
}
```

**Key points:**
- `match` requires exhaustive handling in many languages (a `_`/default case is often mandatory).
- No implicit fall-through — each case is self-contained.
- Supports destructuring, ranges, and guards (extra conditions), making it well suited for structured data.

---

## 6.2 Loops (for, while, do-while)

Loops repeat a block of code while a condition holds or for a fixed number of iterations.

### 6.2.1 `for` Loops

Used when the number of iterations is known or based on a counter/sequence.

```c
// C-style for loop: init; condition; increment
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);
}
```

```python
# Python: iterating over a range or sequence
for i in range(5):
    print(i)

for item in ["a", "b", "c"]:
    print(item)
```

```javascript
// JavaScript
for (let i = 0; i < 5; i++) {
    console.log(i);
}

// for...of iterates values, for...in iterates keys/indices
for (const item of ["a", "b", "c"]) {
    console.log(item);
}
```

**Key points:**
- The classic C-style `for (init; condition; increment)` gives full control over the loop variable.
- Many modern languages favor `for...in` / `for...of` / `for item in collection` to iterate directly over elements, avoiding manual indexing.

### 6.2.2 `while` Loops

Repeats a block as long as a condition remains true. The condition is checked **before** each iteration, so the body may never execute.

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

```javascript
let count = 0;
while (count < 5) {
    console.log(count);
    count++;
}
```

**Use `while` when:** the number of iterations isn't known ahead of time and depends on some runtime condition (e.g., reading input until a sentinel value appears).

### 6.2.3 `do-while` Loops

Similar to `while`, but the condition is checked **after** the body runs, guaranteeing at least one execution.

```c
// C
int count = 0;
do {
    printf("%d\n", count);
    count++;
} while (count < 5);
```

```javascript
// JavaScript
let count = 0;
do {
    console.log(count);
    count++;
} while (count < 5);
```

> Note: Python has no built-in `do-while`. The equivalent pattern is a `while True:` loop with a `break` condition at the end.

```python
count = 0
while True:
    print(count)
    count += 1
    if count >= 5:
        break
```

### 6.2.4 Infinite Loops

A loop with a condition that never becomes false (or is intentionally omitted) runs forever unless stopped internally (e.g., via `break`) or externally.

```python
while True:
    # runs until an explicit break or external interrupt
    ...
```

```c
for (;;) {
    // C idiom for an infinite loop
}
```

Infinite loops are common in event loops, servers, and game loops, but are a bug if unintended — always ensure there's a valid exit path.

---

## 6.3 Break, Continue, and Nested Loops

### 6.3.1 `break`

Immediately exits the nearest enclosing loop (or `switch`), skipping any remaining iterations.

```python
for i in range(10):
    if i == 5:
        break
    print(i)
# Output: 0 1 2 3 4
```

```javascript
for (let i = 0; i < 10; i++) {
    if (i === 5) break;
    console.log(i);
}
```

**Common uses:** stopping a search once a match is found, exiting a loop when an error condition is detected, or terminating an intentionally infinite loop.

### 6.3.2 `continue`

Skips the rest of the current iteration and moves directly to the next one, without exiting the loop entirely.

```python
for i in range(10):
    if i % 2 == 0:
        continue  # skip even numbers
    print(i)
# Output: 1 3 5 7 9
```

```javascript
for (let i = 0; i < 10; i++) {
    if (i % 2 === 0) continue;
    console.log(i);
}
```

**Important:** In a `while`/`do-while` loop, make sure any counter update happens before `continue`, or you risk an infinite loop.

```python
i = 0
while i < 10:
    i += 1
    if i % 2 == 0:
        continue
    print(i)
```

### 6.3.3 Nested Loops

Loops can be placed inside other loops to process multi-dimensional data (grids, matrices, combinations of items).

```python
for row in range(3):
    for col in range(3):
        print(f"({row}, {col})")
```

```javascript
for (let row = 0; row < 3; row++) {
    for (let col = 0; col < 3; col++) {
        console.log(`(${row}, ${col})`);
    }
}
```

By default, `break` and `continue` only affect the **innermost** loop they're placed in.

```python
for i in range(3):
    for j in range(3):
        if j == 1:
            break  # only exits the inner loop
        print(i, j)
```

### 6.3.4 Labeled Break/Continue (Escaping Outer Loops)

Some languages support labels to break or continue an outer loop directly, avoiding flag variables.

```javascript
// JavaScript
outer:
for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        if (j === 1) continue outer; // skips to next i
        if (i === 2) break outer;    // exits both loops
        console.log(i, j);
    }
}
```

```rust
// Rust
'outer: for i in 0..3 {
    for j in 0..3 {
        if j == 1 {
            continue 'outer;
        }
        if i == 2 {
            break 'outer;
        }
        println!("{} {}", i, j);
    }
}
```

**Languages without labels (e.g., Python, C):** the common workaround is a flag variable or refactoring the nested loop into a function and using `return` to exit early.

```python
def find_pair(matrix, target):
    for i, row in enumerate(matrix):
        for j, value in enumerate(row):
            if value == target:
                return (i, j)  # exits both loops at once
    return None
```

### 6.3.5 Best Practices

- Prefer `continue` over deeply nested `if` blocks to reduce indentation and improve readability.
- Use `break` to avoid unnecessary iterations once the goal of a loop is achieved (e.g., searching).
- Avoid overusing labeled breaks/flags — if nested loop logic becomes complex, consider extracting it into a well-named function.
- Always double check loop-ending conditions when using `continue` in `while` loops to prevent infinite loops.
- Keep nesting shallow (generally no more than 2–3 levels) for readability; deeply nested loops are a common refactoring target.

[Previous](./[5]-Core-Syntax-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[7]-Data-Structures.md)
