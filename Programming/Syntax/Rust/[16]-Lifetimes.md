[Previous](./[15]-The-Slice-Type.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[17]-Stack-Heap-and-Copy-vs-Move.md)

*Ownership & Memory Safety*

# Lesson 16 - Lifetimes

## 16.1 What Lifetimes Describe

A lifetime is the compiler's way of tracking **how long a reference is valid**, ensuring it never outlives the data it points to. Most of the time lifetimes are inferred silently — you only write them explicitly when the compiler can't work out the relationship on its own.

```rust
fn longest(x: &str, y: &str) -> &str { // compile error without lifetimes
    if x.len() > y.len() { x } else { y }
}
```

The compiler can't tell whether the returned reference relates to `x` or `y`, so it refuses to compile — it needs to know the returned reference's lifetime is tied to its inputs.

---

## 16.2 Lifetime Annotation Syntax

Lifetime parameters start with an apostrophe, conventionally `'a`, `'b`, etc. They don't change how long anything actually lives — they just describe the relationship between reference lifetimes so the compiler can verify safety:

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

This reads as: "the returned reference is valid for at least as long as the shorter of `x` and `y`'s lifetimes."

---

## 16.3 Lifetimes in Structs

If a struct holds a reference, the struct definition must declare a lifetime, tying the struct's validity to the data it borrows:

```rust
struct Excerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().unwrap();
    let excerpt = Excerpt { part: first_sentence };
    println!("{}", excerpt.part);
}
```

The compiler ensures `excerpt` can't outlive `novel`, since `part` borrows from it.

---

## 16.4 Lifetime Elision

Rust applies a set of predictable rules — "elision rules" — that let you skip explicit lifetimes in common cases, like a function with one reference parameter and one reference return value:

```rust
fn first_word(s: &str) -> &str { // no explicit lifetime needed here
    s.split_whitespace().next().unwrap_or("")
}
```

You only need to write lifetimes explicitly when a function has multiple reference inputs and the compiler can't infer which one the output relates to, as in the `longest` example.

---

## 16.5 The 'static Lifetime

`'static` means a reference is valid for the entire duration of the program — string literals are the most common example, since they're embedded directly in the compiled binary:

```rust
let s: &'static str = "I live for the whole program";
```

Use `'static` sparingly and intentionally — reaching for it to silence a lifetime error usually signals a design issue rather than a genuine fix.

[Previous](./[15]-The-Slice-Type.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[17]-Stack-Heap-and-Copy-vs-Move.md)
