[Previous](./[12]-Comments-and-Documentation.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[14]-Borrowing-and-References.md)

*Ownership & Memory Safety*

# Lesson 13 - Ownership Rules

## 13.1 Why Ownership Exists

Rust has no garbage collector, yet it's memory-safe. It achieves this through **ownership**: a set of compile-time rules the compiler enforces, so there's no runtime cost and no manual `free()` calls like in C.

The three rules are:

1. Each value has exactly one **owner** (a variable).
2. There can only be one owner at a time.
3. When the owner goes out of scope, the value is dropped (its memory freed).

---

## 13.2 Move Semantics

When you assign a heap-allocated value (like a `String`) to another variable, ownership **moves** — the original variable becomes invalid:

```rust
let s1 = String::from("hello");
let s2 = s1; // ownership moves to s2

// println!("{s1}"); // compile error: s1 was moved
println!("{s2}"); // fine
```

This differs from languages where assignment copies or shares a reference silently — Rust makes the transfer explicit and enforces it, preventing two variables from both trying to free the same memory later (a classic C/C++ bug).

Simple stack-only types like integers implement the `Copy` trait instead (see Lesson 29), so they're duplicated rather than moved:

```rust
let x = 5;
let y = x; // x is copied, not moved
println!("{x} {y}"); // both still valid
```

---

## 13.3 Ownership and Functions

Passing a value to a function moves it, just like assignment does, unless the type is `Copy`:

```rust
fn takes_ownership(s: String) {
    println!("{s}");
} // s is dropped here

fn main() {
    let s = String::from("hello");
    takes_ownership(s);
    // s is no longer valid here
}
```

To use a value after passing it to a function, either return it back or — far more commonly — pass a **reference** instead, which is the subject of the next lesson.

---

## 13.4 Ownership and Drop

When an owner goes out of scope, Rust automatically calls `drop`, freeing any heap memory the value held:

```rust
fn main() {
    {
        let s = String::from("scoped");
        println!("{s}");
    } // s's memory is freed right here, deterministically
}
```

This deterministic cleanup — no waiting for a garbage collector to run — is what gives Rust predictable performance alongside its safety guarantees.

[Previous](./[12]-Comments-and-Documentation.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[14]-Borrowing-and-References.md)
