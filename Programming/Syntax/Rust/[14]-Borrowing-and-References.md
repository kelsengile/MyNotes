[Previous](./[13]-Ownership-Rules.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[15]-The-Slice-Type.md)

*Ownership & Memory Safety*

# Lesson 14 - Borrowing And References

## 14.1 References: Access Without Ownership

A reference (`&`) lets you use a value without taking ownership of it — this is called **borrowing**:

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
} // s goes out of scope, but since it doesn't own the value, nothing is dropped

fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);
    println!("The length of '{s1}' is {len}."); // s1 still valid here
}
```

Because `calculate_length` only borrows `s1`, `main` still owns it and can keep using it afterward.

---

## 14.2 Mutable References

By default, references are immutable — you can read through them but not modify what they point to. A `&mut` reference allows modification:

```rust
fn add_exclamation(s: &mut String) {
    s.push_str("!");
}

fn main() {
    let mut s = String::from("hello");
    add_exclamation(&mut s);
    println!("{s}"); // "hello!"
}
```

---

## 14.3 The Borrowing Rules

The compiler enforces these rules at compile time, eliminating data races entirely without any runtime check:

- At any given time, you can have **either** one mutable reference **or** any number of immutable references — never both at once.
- References must always be valid (no dangling references to freed memory).

```rust
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;
println!("{r1} and {r2}"); // fine — multiple immutable borrows

let r3 = &mut s; // error if r1/r2 were still used after this point
```

This rule prevents one part of your code from reading data while another part is simultaneously changing it — a common source of bugs and race conditions in other languages.

---

## 14.4 Dangling References

Rust's compiler refuses to compile code that would produce a dangling reference (a reference to memory that's already been freed):

```rust
fn dangle() -> &String {
    let s = String::from("hello");
    &s
} // s is dropped here, so returning &s is a compile error
```

The fix is to return the owned `String` itself, transferring ownership out, rather than a reference to something that's about to disappear.

[Previous](./[13]-Ownership-Rules.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[15]-The-Slice-Type.md)
