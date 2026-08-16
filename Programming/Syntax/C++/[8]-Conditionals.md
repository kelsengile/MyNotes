[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals

## 8.1 if / else if / else

Conditionals let a program take different paths depending on a boolean expression:

```cpp
int temperature = 72;

if (temperature > 85) {
    std::cout << "It's hot\n";
} else if (temperature > 60) {
    std::cout << "It's mild\n";
} else {
    std::cout << "It's cold\n";
}
```

Only one branch runs: C++ checks each condition in order and executes the first one that's `true`, skipping the rest.

---

## 8.2 switch Statements

A `switch` is useful when comparing one variable against several fixed values:

```cpp
int day = 3;

switch (day) {
    case 1:
        std::cout << "Monday\n";
        break;
    case 2:
        std::cout << "Tuesday\n";
        break;
    case 3:
        std::cout << "Wednesday\n";
        break;
    default:
        std::cout << "Some other day\n";
        break;
}
```

The `break` statement is important — without it, execution **falls through** into the next case, which is sometimes intentional but usually a bug. `default` handles any value not explicitly listed.

---

## 8.3 Ternary Operator

The ternary operator `condition ? valueIfTrue : valueIfFalse` is a compact way to write a simple if/else that produces a value:

```cpp
int age = 20;
std::string label = (age >= 18) ? "adult" : "minor";

// equivalent to:
std::string label2;
if (age >= 18) {
    label2 = "adult";
} else {
    label2 = "minor";
}
```

Use it for short, simple choices — for anything more complex, a regular `if`/`else` is easier to read.

---

## 8.4 Nested Conditionals & Best Practices

Conditionals can be nested inside one another, but deep nesting quickly becomes hard to follow:

```cpp
// Hard to read
if (isLoggedIn) {
    if (hasPermission) {
        if (isActive) {
            std::cout << "Access granted\n";
        }
    }
}

// Easier to read: combine conditions or return early
if (isLoggedIn && hasPermission && isActive) {
    std::cout << "Access granted\n";
}
```

A few habits that keep conditionals readable:

- Prefer combining conditions with `&&`/`||` over nesting when it doesn't hurt clarity.
- Use **early returns** in functions to avoid deeply nested branches.
- Always use braces `{}` for `if` bodies, even single-line ones — it prevents subtle bugs when code is edited later.

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[9]-Loops.md)
