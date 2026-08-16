[Previous](./[22]-Option-and-Result.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[24]-Panic-and-Unwinding.md)

*Error Handling*

# Lesson 23 - The Result Type And The ? Operator

## 23.1 Propagating Errors Manually

Without any shortcuts, propagating a `Result` upward through a call chain means matching on it at every step:

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username() -> Result<String, io::Error> {
    let file_result = File::open("username.txt");

    let mut file = match file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();
    match file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

This works, but it's verbose — the same "return early on error" pattern repeats at every fallible step.

---

## 23.2 The ? Operator

The `?` operator collapses that pattern into one character: if the `Result` is `Ok`, it unwraps the value; if it's `Err`, it returns the error immediately from the enclosing function:

```rust
fn read_username() -> Result<String, io::Error> {
    let mut file = File::open("username.txt")?;
    let mut username = String::new();
    file.read_to_string(&mut username)?;
    Ok(username)
}
```

This is functionally identical to 23.1, but far more readable. `?` can only be used inside a function whose return type is compatible (`Result` or `Option`).

---

## 23.3 ? and Error Conversion

`?` automatically converts the error type using `From`, as long as a conversion exists — useful when a function calls multiple operations that fail with *different* error types but you want to return one unified error type:

```rust
#[derive(Debug)]
struct AppError(String);

impl From<io::Error> for AppError {
    fn from(e: io::Error) -> Self {
        AppError(e.to_string())
    }
}

fn read_username() -> Result<String, AppError> {
    let mut file = File::open("username.txt")?; // io::Error auto-converts to AppError
    let mut username = String::new();
    file.read_to_string(&mut username)?;
    Ok(username)
}
```

---

## 23.4 ? in main

`main` can itself return a `Result`, allowing `?` to be used directly in it — useful for small programs and examples:

```rust
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let username = read_username()?;
    println!("{username}");
    Ok(())
}
```

If `main` returns `Err`, the program exits with a non-zero status code and prints the error using its `Debug` implementation.

[Previous](./[22]-Option-and-Result.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[24]-Panic-and-Unwinding.md)
