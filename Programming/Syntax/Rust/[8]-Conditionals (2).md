[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals: if, else if, else, if let

## 8.1 Basic if / else

Rust's `if` doesn't require parentheses around the condition, but does require curly braces around the branches, and the condition **must** be a `bool` — no implicit truthiness from numbers or strings:

```rust
let number = 7;

if number < 5 {
    println!("small");
} else {
    println!("big");
}
```

---

## 8.2 else if Chains

Chain multiple conditions with `else if`:

```rust
let number = 6;

if number % 4 == 0 {
    println!("divisible by 4");
} else if number % 3 == 0 {
    println!("divisible by 3");
} else if number % 2 == 0 {
    println!("divisible by 2");
} else {
    println!("not divisible by 4, 3, or 2");
}
```

---

## 8.3 if as an Expression

Since `if` is an expression (see Lesson 7), it can produce a value directly:

```rust
let condition = true;
let number = if condition { 5 } else { 6 };
```

Both branches must return the same type, since `number` needs a single, known type.

---

## 8.4 if let for Pattern Matching

`if let` lets you handle a single pattern concisely, which is especially common with `Option` and `Result` (fully covered in Lesson 22):

```rust
let config_max: Option<u8> = Some(3u8);

if let Some(max) = config_max {
    println!("Maximum is configured to be {max}");
} else {
    println!("No maximum configured");
}
```

This is shorthand for a `match` that only cares about one pattern and ignores the rest — compare this to the full `match` expression in Lesson 20.

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[9]-Loops.md)
