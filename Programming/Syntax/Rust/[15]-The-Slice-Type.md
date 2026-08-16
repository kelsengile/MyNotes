[Previous](./[14]-Borrowing-and-References.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[16]-Lifetimes.md)

*Ownership & Memory Safety*

# Lesson 15 - The Slice Type

## 15.1 What Is a Slice?

A slice is a reference to a contiguous sequence of elements within a collection, rather than the whole thing. Slices don't own data — they borrow it, so they follow the same borrowing rules from Lesson 14.

```rust
let s = String::from("hello world");

let hello = &s[0..5];  // "hello"
let world = &s[6..11]; // "world"
```

`0..5` is a range: start inclusive, end exclusive. `&s[..5]` and `&s[6..]` are shorthands for "from the start" and "to the end."

---

## 15.2 String Slices (&str)

As introduced in Lesson 11, `&str` *is* a string slice. This is why functions that only need to read string data should accept `&str` — it works whether the caller has a `String` or a literal:

```rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();
    for (i, &b) in bytes.iter().enumerate() {
        if b == b' ' {
            return &s[0..i];
        }
    }
    s
}
```

Because `first_word` returns a slice *borrowed from* its input, the compiler ensures the caller can't accidentally invalidate it (e.g. by clearing the original string) while the slice is still in use.

---

## 15.3 Array and Vector Slices

Slicing works the same way on arrays and `Vec`s (covered fully in Lesson 21):

```rust
let numbers = [10, 20, 30, 40, 50];
let middle: &[i32] = &numbers[1..4]; // [20, 30, 40]

fn sum(values: &[i32]) -> i32 {
    values.iter().sum()
}

println!("{}", sum(&numbers)); // works on the whole array too
```

`&[i32]` is a slice type — it accepts a reference to an array, a `Vec`, or a sub-range of either, making functions written against slices broadly reusable.

---

## 15.4 Why Slices Matter for Safety

Slices carry both a pointer and a length, and the borrow checker ensures a slice can never outlive the data it points into. This eliminates an entire class of bugs common in C — reading past the end of a buffer, or using a pointer into memory that's already been resized or freed.

[Previous](./[14]-Borrowing-and-References.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[16]-Lifetimes.md)
