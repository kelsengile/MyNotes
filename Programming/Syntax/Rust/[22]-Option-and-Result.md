[Previous](./[21]-Collections.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[23]-Result-Type-and-the-Question-Mark-Operator.md)

*Data Structures*

# Lesson 22 - Option And Result: Handling Absence And Errors

## 22.1 Option Recap

As introduced in Lesson 19, `Option<T>` represents a value that might or might not exist:

```rust
fn find_first_even(numbers: &[i32]) -> Option<i32> {
    for &n in numbers {
        if n % 2 == 0 {
            return Some(n);
        }
    }
    None
}
```

There's no `null` to forget to check — the compiler forces you to handle both `Some` and `None` before extracting the value.

---

## 22.2 Working With Option

```rust
let numbers = [1, 3, 5, 8, 9];
let result = find_first_even(&numbers);

match result {
    Some(n) => println!("Found: {n}"),
    None => println!("No even number found"),
}

// Convenience methods:
let value = result.unwrap_or(0);          // default if None
let doubled = result.map(|n| n * 2);      // transform if Some, else stays None
let unwrapped = result.unwrap();          // panics if None — use carefully
```

`.unwrap()` and `.expect("message")` panic immediately on `None`, so they're best reserved for cases where absence genuinely indicates a bug, or for quick prototyping.

---

## 22.3 The Result Type

`Result<T, E>` represents an operation that can either succeed with a value (`Ok(T)`) or fail with an error (`Err(E)`) — Rust's primary mechanism for recoverable errors:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}

fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err(String::from("cannot divide by zero"))
    } else {
        Ok(a / b)
    }
}
```

---

## 22.4 Handling Results

```rust
match divide(10.0, 2.0) {
    Ok(value) => println!("Result: {value}"),
    Err(e) => println!("Error: {e}"),
}
```

`Result` shares many of the same convenience methods as `Option` — `.unwrap_or()`, `.map()`, `.unwrap()` — and the two types can convert into each other where it makes sense (`.ok()` turns a `Result` into an `Option`, discarding the error). Error handling with `Result` is explored in much greater depth starting in Lesson 23.

[Previous](./[21]-Collections.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[23]-Result-Type-and-the-Question-Mark-Operator.md)
