[Previous](./[25]-Custom-Error-Types.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[27]-Generics.md)

*Traits & Generics*

# Lesson 26 - Traits And Trait Bounds

## 26.1 Defining and Implementing a Trait

A trait defines shared behavior — a set of method signatures that types can implement, similar to interfaces in other languages:

```rust
trait Summary {
    fn summarize(&self) -> String;
}

struct Article {
    headline: String,
    author: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{}, by {}", self.headline, self.author)
    }
}
```

Any type implementing `Summary` is guaranteed to provide `summarize`, letting you write code that works with anything satisfying that trait, rather than one specific type.

---

## 26.2 Default Implementations

Traits can provide default method bodies, which implementing types may use as-is or override:

```rust
trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}

impl Summary for Article {} // uses the default, no override needed
```

---

## 26.3 Traits as Parameters

A function can accept "anything implementing trait X" using `impl Trait` syntax:

```rust
fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

This is sugar for the more explicit **trait bound** syntax, which becomes necessary once you need the same type to appear more than once:

```rust
fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

---

## 26.4 Multiple Trait Bounds

Combine bounds with `+`, and use a `where` clause to keep complex signatures readable:

```rust
use std::fmt::Display;

fn notify<T>(item: &T) -> String
where
    T: Summary + Display,
{
    format!("{item}: {}", item.summarize())
}
```

This requires `T` to implement *both* `Summary` and `Display` — the compiler rejects any type missing either.

---

## 26.5 Returning Types That Implement a Trait

`impl Trait` can also appear in return position, useful when the concrete type is complex or shouldn't be exposed publicly (common with iterators, covered in Lesson 35):

```rust
fn create_summary() -> impl Summary {
    Article {
        headline: String::from("Rust 2.0 Announced"),
        author: String::from("The Rust Team"),
    }
}
```

This has a key limitation: every code path in the function must return the *same* concrete type. Returning different types conditionally requires a trait object (`dyn Trait`), covered in Lesson 28.

[Previous](./[25]-Custom-Error-Types.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[27]-Generics.md)
