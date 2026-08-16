[Previous](./[18]-Structs.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[20]-Match-Expression.md)

*Data Structures*

# Lesson 19 - Enums And Pattern Matching

## 19.1 Defining Enums

An enum defines a type as one of several named variants:

```rust
enum IpAddrKind {
    V4,
    V6,
}

let four = IpAddrKind::V4;
```

---

## 19.2 Enums With Data

Unlike enums in many other languages, Rust enum variants can each carry their own associated data, including different shapes per variant:

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

Enums can even hold structs or other enums as their data, and can have methods defined via `impl`, exactly like structs:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 }, // struct-like variant
    Write(String),
}

impl Message {
    fn call(&self) {
        // method body would typically match on self here
    }
}
```

---

## 19.3 Pattern Matching Basics

The `match` expression (fully covered in Lesson 20) is the primary way to work with enum data — it forces you to handle every variant, so the compiler catches missed cases at compile time:

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

Patterns can also bind and destructure the data a variant carries:

```rust
fn print_coords(msg: Message) {
    if let Message::Move { x, y } = msg {
        println!("Moving to ({x}, {y})");
    }
}
```

---

## 19.4 Option: Rust's Answer to Null

Rust has no `null`. Instead, the standard library's `Option<T>` enum represents a value that might be absent:

```rust
enum Option<T> {
    Some(T),
    None,
}

let some_number: Option<i32> = Some(5);
let no_number: Option<i32> = None;
```

Because `Option<T>` is a distinct type from `T`, the compiler forces you to explicitly handle the "might be absent" case before using the value — eliminating an entire category of null-pointer bugs at compile time. `Option` and its cousin `Result` are covered fully in Lesson 22.

[Previous](./[18]-Structs.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[20]-Match-Expression.md)
