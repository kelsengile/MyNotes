[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[10]-Methods-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops: for, while, do-while, enhanced for, break, continue

Loops let you repeat a block of code without writing it out multiple times. This lesson covers every loop form Java offers.

## 9.1 for Loops

The classic `for` loop is ideal when you know how many times you want to repeat something, or need a counter:

```java
for (int i = 0; i < 5; i++) {
    System.out.println("Count: " + i);
}
```

It has three parts, separated by semicolons: initialization (runs once), condition (checked before each iteration), and update (runs after each iteration).

---

## 9.2 while Loops

A `while` loop repeats as long as its condition stays `true`, checked *before* each iteration. Use it when the number of repetitions isn't known ahead of time:

```java
int count = 0;
while (count < 5) {
    System.out.println("Count: " + count);
    count++;
}
```

If the condition is `false` from the start, the loop body never runs at all.

---

## 9.3 do-while Loops

A `do-while` loop is like `while`, but checks its condition *after* the loop body — guaranteeing the body runs at least once:

```java
int count = 0;
do {
    System.out.println("Count: " + count);
    count++;
} while (count < 5);
```

This is useful for things like input-validation loops, where you need to prompt the user at least one time before checking whether their input was valid.

---

## 9.4 Enhanced for Loop

Also called the "for-each" loop, this form iterates directly over the elements of an array or collection, without needing an index variable:

```java
int[] numbers = {1, 2, 3, 4, 5};
for (int n : numbers) {
    System.out.println(n);
}
```

It's more concise and less error-prone than a manual index-based loop, but you lose access to the index itself — use a regular `for` loop if you need it.

---

## 9.5 break and continue

- **`break`** exits the loop immediately, skipping any remaining iterations.
- **`continue`** skips the rest of the current iteration and moves on to the next one.

```java
for (int i = 0; i < 10; i++) {
    if (i == 5) break;      // stops the loop entirely at i == 5
    if (i % 2 == 0) continue; // skips even numbers
    System.out.println(i);
}
```

---

## 9.6 Nested Loops and Labels

Loops can be nested inside one another, which is common when working with grids or multidimensional data. By default, `break` and `continue` only affect the *innermost* loop — but Java lets you attach a **label** to an outer loop to control it directly:

```java
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) continue outer;
        System.out.println(i + "," + j);
    }
}
```

Labeled `break`/`continue` are used sparingly, but are helpful for breaking out of deeply nested loops cleanly.

---

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[10]-Methods-and-Scope.md)