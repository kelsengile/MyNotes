[Previous](./[16]-Lifetimes.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[18]-Structs.md)

*Ownership & Memory Safety*

# Lesson 17 - The Stack, The Heap And Copy vs. Move Semantics

## 17.1 The Stack

The stack stores values in a strict last-in-first-out order and requires every value to have a known, fixed size at compile time. Pushing and popping from the stack is extremely fast. Simple types like `i32`, `bool`, `char`, and fixed-size arrays typically live entirely on the stack.

```rust
fn main() {
    let a = 5;      // stack
    let b = [1, 2, 3]; // fixed size, also stack
}
```

---

## 17.2 The Heap

The heap stores data whose size may be unknown at compile time or that needs to grow — like a `String` or `Vec`. Allocating on the heap is slower than pushing to the stack, and requires a pointer (kept on the stack) referring to the heap location:

```rust
fn main() {
    let s = String::from("hello"); // pointer, length, capacity on stack;
                                     // the actual bytes "hello" on the heap
}
```

Ownership (Lesson 13) exists primarily to manage heap allocations correctly without a garbage collector — the stack-only case is comparatively simple.

---

## 17.3 Why Some Types Copy and Others Move

Recall from Lesson 13 that assigning one variable to another either **moves** or **copies** the value, depending on its type:

- Types that are simple, fixed-size, and live entirely on the stack (integers, floats, `bool`, `char`, and tuples of these) implement the `Copy` trait — assignment duplicates the value cheaply.
- Types that manage heap data (`String`, `Vec`, `Box`, etc.) do **not** implement `Copy` — assignment moves ownership instead, since duplicating the pointer without duplicating the heap data would leave two owners responsible for the same memory.

```rust
let x = 5;
let y = x; // Copy: cheap, both valid

let s1 = String::from("hi");
let s2 = s1; // Move: s1 is no longer valid
```

---

## 17.4 Explicit Clones

When you genuinely want a deep copy of heap data — a second, independent allocation — call `.clone()` explicitly:

```rust
let s1 = String::from("hi");
let s2 = s1.clone(); // both s1 and s2 are valid, backed by separate memory
println!("{s1} {s2}");
```

Rust makes this cost visible and intentional (`.clone()` must be written out) rather than happening silently on every assignment, which keeps performance characteristics predictable at a glance.

[Previous](./[16]-Lifetimes.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[18]-Structs.md)
