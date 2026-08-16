[Previous](./[29]-Common-Standard-Traits.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[31]-Modules-Paths-and-Visibility.md)

*Traits & Generics*

# Lesson 30 - Operator Overloading (std::ops)

## 30.1 Operators Are Traits

In Rust, operators like `+`, `-`, and `==` aren't special syntax reserved for built-in types — they're backed by traits in `std::ops` (and `PartialEq`/`PartialOrd`, from Lesson 29). Implementing the right trait lets your own types use these operators naturally.

---

## 30.2 Implementing Add

The `Add` trait powers the `+` operator:

```rust
use std::ops::Add;

#[derive(Debug, Clone, Copy)]
struct Point {
    x: i32,
    y: i32,
}

impl Add for Point {
    type Output = Point;

    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = Point { x: 3, y: 4 };
    let p3 = p1 + p2; // calls Point::add
    println!("{p3:?}"); // Point { x: 4, y: 6 }
}
```

`type Output` specifies what type the operation produces — usually, but not always, `Self`.

---

## 30.3 Other Common Operator Traits

The same pattern applies across `std::ops`:

| Operator | Trait |
|---|---|
| `+` | `Add` |
| `-` | `Sub` |
| `*` | `Mul` |
| `/` | `Div` |
| `-x` (unary negation) | `Neg` |
| `[]` indexing | `Index` / `IndexMut` |

```rust
use std::ops::Mul;

impl Mul<i32> for Point {
    type Output = Point;

    fn mul(self, scalar: i32) -> Point {
        Point { x: self.x * scalar, y: self.y * scalar }
    }
}

let doubled = Point { x: 1, y: 2 } * 2; // Point { x: 2, y: 4 }
```

Note `Mul<i32>` here — operator traits are generic over the right-hand operand's type, so a `Point` can be multiplied by an `i32` even though the trait's `Self` is `Point`.

---

## 30.4 When to Overload Operators

Operator overloading is best reserved for types where the operator has an unambiguous, mathematically natural meaning — vectors, matrices, complex numbers, durations. Overloading `+` for a type where "addition" isn't obvious to a reader (e.g., merging two unrelated structs) tends to make code harder to understand rather than easier; a clearly named method is usually the better choice in those cases.

[Previous](./[29]-Common-Standard-Traits.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[31]-Modules-Paths-and-Visibility.md)
