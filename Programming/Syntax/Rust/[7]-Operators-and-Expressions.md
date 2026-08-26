[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators And Expressions

## 7.1 Arithmetic and Comparison Operators

Standard arithmetic operators work as expected: `+`, `-`, `*`, `/`, `%` (remainder).

```rust
let sum = 5 + 3;
let remainder = 10 % 3; // 1
```

Comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`) return a `bool`, and logical operators (`&&`, `||`, `!`) combine them:

```rust
let is_valid = (5 > 3) && (10 != 11); // true
```

Rust does **not** implicitly convert between types in comparisons or arithmetic — comparing an `i32` to a `u32` directly is a compile error, and one side must be explicitly cast.

---

## 7.2 Compound Assignment

Shorthand operators mutate a variable in place (requires `mut`):

```rust
let mut total = 10;
total += 5;  // 15
total -= 2;  // 13
total *= 3;  // 39
```

---

## 7.3 Everything Is an Expression

Most constructs in Rust — `if`, blocks, `match`, loops with `break` — are **expressions** that evaluate to a value, not just statements. A block's final line, if it has no semicolon, becomes its value:

```rust
let y = {
    let x = 3;
    x + 1   // no semicolon — this is the block's value
}; // y is 4
```

This is why `if` can be used directly in an assignment:

```rust
let condition = true;
let number = if condition { 5 } else { 6 };
```

Both branches must produce the same type for this to compile.

---

## 7.4 Statements vs. Expressions

A **statement** performs an action and returns nothing (`let x = 5;` is a statement). An **expression** evaluates to a value (`5 + 6` is an expression). Adding a semicolon turns an expression into a statement by discarding its value — a common mistake is accidentally adding a semicolon to a function's final line, turning it into `()` (the "unit" type, Rust's version of "nothing") instead of returning the intended value.

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[8]-Conditionals.md)
