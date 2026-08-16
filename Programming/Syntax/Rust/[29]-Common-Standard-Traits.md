[Previous](./[28]-Trait-Objects.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[30]-Operator-Overloading.md)

*Traits & Generics*

# Lesson 29 - Common Standard Traits (Clone, Copy, Debug, PartialEq, Iterator)

## 29.1 Deriving Traits

Many standard traits have mechanical, predictable implementations the compiler can generate for you with `#[derive(...)]`, saving you from writing repetitive boilerplate by hand:

```rust
#[derive(Debug, Clone, Copy, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}
```

This single attribute implements all four traits below for `Point` automatically, as long as every field also implements them.

---

## 29.2 Debug and Display

`Debug` (derivable) enables printing a value with `{:?}` — meant for developers, showing internal structure:

```rust
#[derive(Debug)]
struct Point { x: i32, y: i32 }

let p = Point { x: 1, y: 2 };
println!("{:?}", p);  // Point { x: 1, y: 2 }
println!("{:#?}", p); // pretty-printed, multi-line version
```

`Display` (not derivable — must be hand-written, as shown in Lesson 25) enables `{}` formatting, meant for end users, and requires you to define exactly what the output should look like.

---

## 29.3 Clone and Copy, Revisited

As covered in Lessons 13 and 17:

- **`Clone`** provides `.clone()`, an explicit deep copy — required for any type that manages heap data.
- **`Copy`** makes assignment implicitly duplicate the value instead of moving it — only valid for simple, fixed-size, stack-only types. `Copy` requires `Clone` also be implemented.

```rust
#[derive(Clone, Copy)]
struct Coordinates { x: i32, y: i32 } // fine — both fields are Copy

#[derive(Clone)] // Copy not possible: String isn't Copy
struct Name { value: String }
```

---

## 29.4 PartialEq and Eq

`PartialEq` enables `==` and `!=` comparisons; deriving it compares every field for equality:

```rust
#[derive(PartialEq)]
struct Point { x: i32, y: i32 }

let a = Point { x: 1, y: 2 };
let b = Point { x: 1, y: 2 };
println!("{}", a == b); // true
```

`Eq` is a marker trait (no methods of its own) layered on top of `PartialEq`, asserting that equality is *total* — every value equals itself, with no exceptions. Floats implement `PartialEq` but not `Eq`, because `NaN != NaN`.

---

## 29.5 The Iterator Trait

`Iterator` powers `for` loops (Lesson 9) and the whole iterator-adapter ecosystem (`map`, `filter`, `collect`, etc., covered fully in Lesson 35). Implementing it requires just one method, `next`:

```rust
struct Counter { count: u32 }

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<u32> {
        if self.count < 5 {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}
```

Once `next` is defined, dozens of other methods (`sum`, `collect`, `map`, `filter`, ...) become available automatically, since they're provided as default implementations built on top of `next`.

[Previous](./[28]-Trait-Objects.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[30]-Operator-Overloading.md)
