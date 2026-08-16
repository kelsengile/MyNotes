[Previous](./[6]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[8]-Loops.md)

*Core Syntax*

# Lesson 7 - Conditionals

## 7.1 The if Statement

An `if` statement runs a block of code only when its condition is true (non-zero):

```c
int age = 20;

if (age >= 18) {
    printf("You are an adult.\n");
}
```

Any non-zero value is treated as true; only `0` is treated as false. This means expressions like `if (pointer)` (true if `pointer` is not `NULL`) are idiomatic C.

---

## 7.2 else and else if

`else` runs when the `if` condition is false. Chain `else if` to test multiple conditions in sequence:

```c
int score = 72;

if (score >= 90) {
    printf("A\n");
} else if (score >= 80) {
    printf("B\n");
} else if (score >= 70) {
    printf("C\n");
} else {
    printf("F\n");
}
```

Conditions are checked top to bottom, and only the first matching branch runs — the rest are skipped even if they'd also be true.

---

## 7.3 The switch Statement

`switch` compares a single value against several constant cases — often clearer than a long `if`/`else if` chain when checking one variable against many fixed values:

```c
int day = 3;

switch (day) {
    case 1:
        printf("Monday\n");
        break;
    case 2:
        printf("Tuesday\n");
        break;
    case 3:
        printf("Wednesday\n");
        break;
    default:
        printf("Some other day\n");
        break;
}
```

The `break` statement is critical: without it, execution **falls through** into the next case, which is sometimes intentional but usually a bug:

```c
switch (day) {
    case 1:
    case 2:
    case 3:
    case 4:
    case 5:
        printf("Weekday\n");  // matches any of cases 1-5, falls through to here
        break;
    case 6:
    case 7:
        printf("Weekend\n");
        break;
}
```

`switch` only works with integer or `char` types (and enums, covered in Lesson 19) — not floats or strings.

---

## 7.4 The Ternary Operator

The ternary operator `?:` is a compact form of `if`/`else` that produces a value:

```c
int a = 10, b = 20;
int max = (a > b) ? a : b;   // max = 20
```

It's best reserved for short, simple expressions. For anything with side effects or multiple lines, a regular `if`/`else` is more readable:

```c
// Good use:
const char *label = (count == 1) ? "item" : "items";

// Avoid nesting ternaries -- this is hard to read:
int result = (a > b) ? ((a > c) ? a : c) : ((b > c) ? b : c);
```

---

[Previous](./[6]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[8]-Loops.md)
