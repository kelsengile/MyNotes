[Previous](./[4]-Project-Structure-and-Workspaces.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables, Mutability And Basic Data Types

## 5.1 Variables Are Immutable by Default

Unlike most languages, variables in Rust are **immutable unless you say otherwise**. This is a deliberate safety choice.

```rust
let x = 5;
x = 6; // compile error: cannot assign twice to immutable variable
```

To allow reassignment, add `mut`:

```rust
let mut x = 5;
x = 6; // fine
```

Immutability by default pushes you to only opt into mutable state where you actually need it, which makes code easier to reason about.

---

## 5.2 Shadowing

You can declare a new variable with the *same name*, which "shadows" the old one:

```rust
let x = 5;
let x = x + 1;   // x is now 6
let x = x * 2;   // x is now 12
```

Shadowing differs from `mut`: it creates an entirely new binding (which can even change type), rather than mutating the original.

```rust
let spaces = "   ";
let spaces = spaces.len(); // now a number, not a string — allowed via shadowing
```

---

## 5.3 Constants

`const` values must be known at compile time, can never be mutable, and require an explicit type annotation:

```rust
const MAX_POINTS: u32 = 100_000;
```

Constants can be declared in any scope, including global scope, and are inlined wherever used.

---

## 5.4 Basic Data Types Overview

Rust is statically typed — every value has a known type, usually inferred by the compiler. The core scalar types are:

| Category | Examples |
|---|---|
| Integers | `i32`, `u32`, `i64`, `u8`, ... |
| Floating-point | `f32`, `f64` |
| Boolean | `bool` |
| Character | `char` |

```rust
let age: u8 = 30;
let pi: f64 = 3.14159;
let is_active: bool = true;
let grade: char = 'A';
```

Numbers, strings, and booleans are covered in depth in the next lesson.

[Previous](./[4]-Project-Structure-and-Workspaces.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)
