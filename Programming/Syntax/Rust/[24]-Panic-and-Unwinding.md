[Previous](./[23]-Result-Type-and-the-Question-Mark-Operator.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[25]-Custom-Error-Types.md)

*Error Handling*

# Lesson 24 - Panic And Unwinding

## 24.1 What a Panic Is

A **panic** is Rust's response to an unrecoverable error — a bug the program can't reasonably continue past, like an out-of-bounds index or an explicit `.unwrap()` on a `None`. Panics are for programming errors, not expected failure conditions (which should use `Result`, covered in Lesson 23):

```rust
fn main() {
    let v = vec![1, 2, 3];
    println!("{}", v[10]); // panics: index out of bounds
}
```

You can trigger a panic directly with the `panic!` macro:

```rust
fn check_age(age: i32) {
    if age < 0 {
        panic!("age cannot be negative: {age}");
    }
}
```

---

## 24.2 Unwinding vs. Aborting

By default, a panic **unwinds** the stack — Rust walks back up through the call stack, running `drop` on each value along the way to clean up memory, then exits the thread (or process, if it's the main thread).

Alternatively, a project can be configured to **abort** immediately instead, skipping cleanup, which produces a smaller binary and faster panics at the cost of no cleanup:

```toml
# Cargo.toml
[profile.release]
panic = "abort"
```

---

## 24.3 Catching Panics

Panics can be caught using `std::panic::catch_unwind`, though this is uncommon in application code — it's mostly used in contexts like test harnesses or plugin systems where one failing unit shouldn't take down the whole process:

```rust
let result = std::panic::catch_unwind(|| {
    panic!("something went wrong");
});

if result.is_err() {
    println!("Recovered from a panic");
}
```

Reaching for `catch_unwind` as a general error-handling strategy is considered an anti-pattern — `Result` and `?` (Lesson 23) are the idiomatic tools for errors you expect to happen and want to recover from.

---

## 24.4 When to Panic vs. Return Result

A useful rule of thumb:

- **Panic** when continuing would be unsafe or meaningless — an invariant your code relies on has been violated, indicating a bug.
- **Return `Result`** when failure is a normal, expected outcome the caller should be able to handle — a missing file, invalid user input, a failed network request.

Library code in particular should almost always prefer `Result` over panicking, since a library can't know whether its caller considers a given failure recoverable.

[Previous](./[23]-Result-Type-and-the-Question-Mark-Operator.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[25]-Custom-Error-Types.md)
