[Previous](./[7]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[9]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 8 - Loops

## 8.1 The for Loop

`for` is best when you know how many iterations you need, such as counting through an array:

```c
for (int i = 0; i < 5; i++) {
    printf("i = %d\n", i);
}
```

A `for` loop has three parts, separated by semicolons:

1. **Initialization** (`int i = 0`) — runs once, before the loop starts.
2. **Condition** (`i < 5`) — checked before every iteration; the loop stops once it's false.
3. **Update** (`i++`) — runs after every iteration.

Any of the three parts can be left empty, and all variables used remain in scope only within the loop body when declared in the initializer.

---

## 8.2 The while Loop

`while` is best when the number of iterations isn't known ahead of time — the loop continues as long as its condition holds:

```c
int count = 0;
while (count < 3) {
    printf("count = %d\n", count);
    count++;
}
```

The condition is checked **before** each iteration, so if it's false to start, the body never runs at all:

```c
int x = 10;
while (x < 5) {
    printf("never printed\n");
}
```

---

## 8.3 The do-while Loop

`do-while` is like `while`, but checks its condition **after** running the body — guaranteeing the body runs at least once:

```c
int input;
do {
    printf("Enter a positive number: ");
    scanf("%d", &input);
} while (input <= 0);
```

This pattern is common for input validation, where you need to prompt the user at least once before you have anything to check.

---

## 8.4 break and continue

`break` exits the loop immediately:

```c
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;   // stops the loop entirely once i reaches 5
    }
    printf("%d\n", i);   // prints 0 1 2 3 4
}
```

`continue` skips the rest of the current iteration and jumps to the next one:

```c
for (int i = 0; i < 5; i++) {
    if (i % 2 == 0) {
        continue;   // skip even numbers
    }
    printf("%d\n", i);   // prints 1 3
}
```

---

## 8.5 Nested Loops

Loops can be nested inside one another — useful for anything grid-like, such as processing a 2D array:

```c
for (int row = 0; row < 3; row++) {
    for (int col = 0; col < 3; col++) {
        printf("(%d,%d) ", row, col);
    }
    printf("\n");
}
```

`break` and `continue` only affect the **innermost** loop they're written in. To exit multiple nested loops at once, a common approach is a flag variable or a `goto` (used sparingly, and covered further in Lesson 45):

```c
int found = 0;
for (int i = 0; i < 10 && !found; i++) {
    for (int j = 0; j < 10; j++) {
        if (matrix[i][j] == target) {
            found = 1;
            break;   // only exits the inner loop
        }
    }
}
```

---

[Previous](./[7]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[9]-Functions-and-Scope.md)
