[Previous](./[5]-Variables-Mutability-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings And Booleans

## 6.1 Integer Types

Rust gives you precise control over integer size and signedness: `i8`/`u8` through `i128`/`u128`, plus `isize`/`usize` (sized to match the platform's pointer width, commonly used for indexing collections).

```rust
let a: i32 = -42;      // signed, default integer type
let b: u32 = 42;       // unsigned, cannot be negative
let c: usize = 10;     // used for array/vector indices and lengths
```

If you don't annotate a type, Rust defaults to `i32`. Arithmetic that overflows a type panics in debug builds and wraps silently in release builds — see Lesson 24 for more on panics.

---

## 6.2 Floating-Point Types

`f32` and `f64` represent decimal numbers; `f64` is the default and offers more precision:

```rust
let price: f64 = 19.99;
let discount: f32 = 0.15;
```

---

## 6.3 Booleans and Characters

`bool` is either `true` or `false`, one byte in size:

```rust
let logged_in: bool = false;
```

`char` represents a single Unicode scalar value (4 bytes), written with single quotes — so it can hold far more than ASCII:

```rust
let heart: char = '❤';
let letter: char = 'R';
```

---

## 6.4 Strings: A First Look

Rust has two primary string types, covered fully in Lesson 11:

- **`String`** — an owned, growable, heap-allocated string.
- **`&str`** — a borrowed "string slice," often a view into a `String` or a literal.

```rust
let greeting: &str = "Hello";        // string literal, a &str
let mut name: String = String::from("Rust"); // owned, growable
name.push_str(" Lang");
```

String literals like `"Hello"` are `&str` by default because their contents are baked into the compiled binary and never need to change size.

[Previous](./[5]-Variables-Mutability-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[7]-Operators-and-Expressions.md)
