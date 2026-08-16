[Previous](./[24]-Panic-and-Unwinding.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[26]-Traits-and-Trait-Bounds.md)

*Error Handling*

# Lesson 25 - Custom Error Types And Error Handling Crates

## 25.1 Writing a Custom Error Enum

For anything beyond trivial programs, a dedicated error enum communicates the different ways a function can fail far better than a generic `String`:

```rust
use std::fmt;

#[derive(Debug)]
enum ConfigError {
    MissingField(String),
    InvalidValue { field: String, value: String },
}

impl fmt::Display for ConfigError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            ConfigError::MissingField(name) => write!(f, "missing field: {name}"),
            ConfigError::InvalidValue { field, value } =>
                write!(f, "invalid value '{value}' for field '{field}'"),
        }
    }
}

impl std::error::Error for ConfigError {}
```

Implementing both `Debug` (usually derived) and `Display`, plus the `std::error::Error` trait, makes your type a proper "error type" that plays well with the rest of the ecosystem — including the `?` operator from Lesson 23.

---

## 25.2 thiserror: Less Boilerplate

Writing `Display` impls by hand for every error type gets repetitive. The `thiserror` crate generates it via a derive macro:

```rust
use thiserror::Error;

#[derive(Error, Debug)]
enum ConfigError {
    #[error("missing field: {0}")]
    MissingField(String),

    #[error("invalid value '{value}' for field '{field}'")]
    InvalidValue { field: String, value: String },
}
```

This produces the same functionality as 25.1's hand-written version, with far less code. `thiserror` is the standard choice for **libraries**, where callers need a precise, typed error to match against.

---

## 25.3 anyhow: Flexible Application Errors

`anyhow` takes the opposite approach: instead of precise typed errors, it provides a single `anyhow::Error` type that can wrap *any* error, ideal for **applications** where the caller (often just `main`) doesn't need to match on specific error variants — only report and exit:

```rust
use anyhow::{Context, Result};

fn read_config() -> Result<String> {
    let contents = std::fs::read_to_string("config.toml")
        .context("failed to read config file")?;
    Ok(contents)
}
```

`.context(...)` attaches a human-readable message as the error propagates upward, building a helpful chain of "what was happening when this failed."

---

## 25.4 Choosing Between Them

A common convention: use `thiserror` for library crates (so consumers get precise, matchable error types) and `anyhow` for application/binary crates (so you get ergonomic error propagation without defining a type for every possible failure). Many projects use both together — `thiserror` internally, wrapped in `anyhow::Result` at the application boundary.

[Previous](./[24]-Panic-and-Unwinding.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[26]-Traits-and-Trait-Bounds.md)
