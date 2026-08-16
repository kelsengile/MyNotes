[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[13]-Ownership-Rules.md)

*Core Syntax*

# Lesson 12 - Comments And Documentation

## 12.1 Regular Comments

Line comments start with `//`; block comments use `/* ... */`:

```rust
// This is a single-line comment
let x = 5; // comments can also trail code

/*
  This is a block comment,
  useful for longer explanations.
*/
```

---

## 12.2 Doc Comments

Doc comments use `///` above an item (function, struct, etc.) and support Markdown. Unlike regular comments, they're extracted into generated documentation:

```rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// let result = my_crate::add(2, 3);
/// assert_eq!(result, 5);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

`//!` is used for **inner** doc comments, documenting the enclosing item (commonly placed at the top of a file to document the whole module or crate):

```rust
//! This module provides basic arithmetic helpers.
```

---

## 12.3 Generating Docs With rustdoc

Running:

```bash
cargo doc --open
```

builds an HTML documentation site from all doc comments in your project and its dependencies, then opens it in your browser. This is the same tool that generates the documentation pages on [docs.rs](https://docs.rs) for every crate published to crates.io.

---

## 12.4 Doc-Tests

Code blocks inside doc comments (like the `add` example in 12.2) are automatically run as tests:

```bash
cargo test --doc
```

This keeps documentation honest — if the example code stops compiling or its `assert_eq!` fails, `cargo test` fails too, catching outdated docs before they mislead anyone. Testing in general is covered fully in Lesson 75.

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[13]-Ownership-Rules.md)
