[Previous](./[19]-Enums-and-Pattern-Matching.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[21]-Collections.md)

*Data Structures*

# Lesson 20 - The match Expression And if let / while let

## 20.1 match Basics

`match` compares a value against a series of patterns and runs the code for the first one that fits. It's exhaustive: the compiler requires every possible case to be handled:

```rust
fn describe(n: i32) -> &'static str {
    match n {
        0 => "zero",
        1 | 2 | 3 => "small",       // multiple patterns with |
        4..=9 => "medium",           // inclusive range pattern
        _ => "large",                // catch-all
    }
}
```

The `_` pattern matches anything not already covered, and is required unless every possibility (like every enum variant) is explicitly listed.

---

## 20.2 Matching and Binding Values

Patterns can extract data out of what they match, binding it to a name usable in that arm:

```rust
enum Shape {
    Circle(f64),
    Rectangle(f64, f64),
}

fn area(shape: Shape) -> f64 {
    match shape {
        Shape::Circle(radius) => std::f64::consts::PI * radius * radius,
        Shape::Rectangle(w, h) => w * h,
    }
}
```

---

## 20.3 Match Guards

A **match guard** adds an extra `if` condition to a pattern, for cases a pattern alone can't express:

```rust
let pair = (5, -5);

match pair {
    (x, y) if x == -y => println!("These are opposites"),
    (x, _) if x % 2 == 0 => println!("The first is even"),
    _ => println!("No particular relationship"),
}
```

---

## 20.4 if let and while let

`if let` (introduced in Lesson 8) is shorthand for a `match` that only cares about one pattern:

```rust
let config: Option<u8> = Some(3);

if let Some(max) = config {
    println!("Max is {max}");
}
```

`while let` loops as long as a pattern keeps matching — commonly used to drain a collection or channel item by item:

```rust
let mut stack = vec![1, 2, 3];

while let Some(top) = stack.pop() {
    println!("{top}");
}
```

Reach for `match` when you need to handle multiple patterns exhaustively, and `if let`/`while let` when you only care about one pattern and want to ignore the rest concisely.

[Previous](./[19]-Enums-and-Pattern-Matching.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[21]-Collections.md)
