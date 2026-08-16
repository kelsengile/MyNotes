[Previous](./[26]-Traits-and-Trait-Bounds.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[28]-Trait-Objects.md)

*Traits & Generics*

# Lesson 27 - Generics

## 27.1 Why Generics?

Generics let you write code once that works over many types, avoiding duplication. Compare a function locked to one type with a generic version:

```rust
fn largest_i32(list: &[i32]) -> &i32 {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}
```

`largest` now works for `&[i32]`, `&[f64]`, `&[char]` — anything implementing `PartialOrd` (the trait enabling `>` comparisons).

---

## 27.2 Generic Structs and Enums

Structs and enums can be generic over one or more type parameters:

```rust
struct Point<T> {
    x: T,
    y: T,
}

let integer_point = Point { x: 5, y: 10 };
let float_point = Point { x: 1.0, y: 4.0 };
```

Use multiple type parameters if the fields don't need to share a type:

```rust
struct Pair<T, U> {
    first: T,
    second: U,
}
```

`Option<T>` and `Result<T, E>`, seen in Lessons 19 and 22, are themselves generic enums defined exactly this way in the standard library.

---

## 27.3 Generic Methods

`impl` blocks can be generic too, and can add bounds specific to individual methods:

```rust
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

impl<T: std::fmt::Display> Point<T> {
    fn print(&self) {
        println!("({}, {})", self.x, self.y);
    }
}
```

Here, `print` is only available on `Point<T>` where `T` implements `Display` — `Point<SomeNonDisplayType>` simply won't have that method.

---

## 27.4 Zero-Cost Abstraction

Generics in Rust have no runtime cost. The compiler performs **monomorphization**: for every concrete type a generic function or struct is used with, it generates a specialized version at compile time, exactly as if you'd hand-written it for each type. This means generic code runs exactly as fast as duplicated, type-specific code — you get the flexibility with no performance penalty. This principle is revisited in Lesson 88.

[Previous](./[26]-Traits-and-Trait-Bounds.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[28]-Trait-Objects.md)
