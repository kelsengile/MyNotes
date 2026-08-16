[Previous](./[27]-Generics.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[29]-Common-Standard-Traits.md)

*Traits & Generics*

# Lesson 28 - Default Trait Implementations And Trait Objects (dyn)

## 28.1 Default Implementations, Revisited

As introduced in Lesson 26, traits can supply default method bodies, so implementers only need to override what's different:

```rust
trait Greet {
    fn name(&self) -> String;

    fn greet(&self) -> String {
        format!("Hello, {}!", self.name())
    }
}

struct Person { name: String }

impl Greet for Person {
    fn name(&self) -> String {
        self.name.clone()
    }
    // greet() uses the default implementation
}
```

Default methods can call other (required) methods on the same trait, even though only the trait author knows the default body — the implementer just needs to supply `name`.

---

## 28.2 The Problem Trait Objects Solve

Generics (Lesson 27) resolve to one concrete type at compile time per call site. But sometimes you need a single collection holding *different* concrete types that all share a trait — e.g., a list of UI components. Generics can't express that; **trait objects** can.

---

## 28.3 dyn Trait

`dyn Trait` is a trait object: a way to refer to "some type implementing this trait" whose exact type is only known at runtime. Trait objects are always used behind a pointer — `&dyn Trait` or `Box<dyn Trait>` — since their size isn't known at compile time:

```rust
trait Shape {
    fn area(&self) -> f64;
}

struct Circle { radius: f64 }
struct Square { side: f64 }

impl Shape for Circle {
    fn area(&self) -> f64 { std::f64::consts::PI * self.radius * self.radius }
}
impl Shape for Square {
    fn area(&self) -> f64 { self.side * self.side }
}

fn main() {
    let shapes: Vec<Box<dyn Shape>> = vec![
        Box::new(Circle { radius: 2.0 }),
        Box::new(Square { side: 3.0 }),
    ];

    for shape in &shapes {
        println!("{}", shape.area());
    }
}
```

`Box<dyn Shape>` heap-allocates each value and stores a pointer to it, allowing genuinely different concrete types to sit in the same `Vec`.

---

## 28.4 Static vs. Dynamic Dispatch

- **Static dispatch** (generics with trait bounds) — the compiler knows the exact type at compile time and generates specialized code per type (monomorphization). Faster, but each generic function call site is fixed to one type.
- **Dynamic dispatch** (`dyn Trait`) — the concrete type is resolved at runtime via a vtable (a lookup table of function pointers), adding a small runtime cost but allowing heterogeneous collections and more flexible APIs.

A common rule of thumb: default to generics for performance-sensitive code with a known type at each call site, and reach for `dyn Trait` when you genuinely need runtime polymorphism — a mixed collection, or a plugin-style system where types aren't known until runtime.

[Previous](./[27]-Generics.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[29]-Common-Standard-Traits.md)
