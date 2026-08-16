[Previous](./[17]-Stack-Heap-and-Copy-vs-Move.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[19]-Enums-and-Pattern-Matching.md)

*Data Structures*

# Lesson 18 - Structs

## 18.1 Defining and Instantiating Structs

A struct groups related named fields into a single custom type:

```rust
struct User {
    username: String,
    email: String,
    active: bool,
}

fn main() {
    let user1 = User {
        username: String::from("rustacean"),
        email: String::from("user@example.com"),
        active: true,
    };

    println!("{}", user1.username);
}
```

To modify a field, the whole instance must be marked `mut` — Rust doesn't allow marking individual fields as mutable:

```rust
let mut user1 = User { /* ... */ };
user1.email = String::from("new@example.com");
```

---

## 18.2 Field Init Shorthand and Update Syntax

When a variable name matches a field name, you can skip repeating it:

```rust
fn build_user(username: String, email: String) -> User {
    User { username, email, active: true } // shorthand
}
```

Struct update syntax creates a new instance from an existing one, overriding only specific fields:

```rust
let user2 = User {
    email: String::from("another@example.com"),
    ..user1 // fill the rest from user1
};
```

Note this may move fields (like `username`, a `String`) out of `user1`, so `user1` might not be usable afterward unless those fields are `Copy` types.

---

## 18.3 Tuple Structs and Unit Structs

**Tuple structs** have fields without names, useful for giving a tuple a distinct type:

```rust
struct Point(f64, f64);
let origin = Point(0.0, 0.0);
println!("{}", origin.0);
```

**Unit structs** have no fields at all, often used as markers or to implement a trait on:

```rust
struct AlwaysEqual;
let subject = AlwaysEqual;
```

---

## 18.4 Methods With impl

Behavior is attached to structs via `impl` blocks. Methods take `self` (in some form) as their first parameter; **associated functions** don't, and are commonly used as constructors:

```rust
struct Rectangle {
    width: f64,
    height: f64,
}

impl Rectangle {
    fn new(width: f64, height: f64) -> Self {
        Self { width, height }
    }

    fn area(&self) -> f64 {
        self.width * self.height
    }
}

fn main() {
    let rect = Rectangle::new(30.0, 50.0);
    println!("Area: {}", rect.area());
}
```

`&self` borrows the instance immutably; `&mut self` borrows it mutably for methods that modify fields; and plain `self` takes ownership, consuming the instance (uncommon, but used for builder-style APIs).

[Previous](./[17]-Stack-Heap-and-Copy-vs-Move.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[19]-Enums-and-Pattern-Matching.md)
