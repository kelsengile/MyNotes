[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[12]-Comments-and-Documentation.md)

*Core Syntax*

# Lesson 11 - String Formatting And Manipulation

## 11.1 String vs. &str, Revisited

`String` owns its data on the heap and can grow or shrink; `&str` is a borrowed, immutable *view* into string data — either part of a `String` or a literal baked into the binary. A `&str` is often called a "string slice."

```rust
let owned: String = String::from("hello");
let borrowed: &str = &owned;      // borrowing a String as a &str
let literal: &str = "world";      // a &str from the start
```

Functions that only need to *read* a string should accept `&str` — it accepts both `&String` and string literals, making the function more flexible.

---

## 11.2 The format! Macro and println!

`format!` builds a new `String` using placeholders; `println!` does the same but prints directly:

```rust
let name = "Rust";
let version = 1.82;

let message = format!("Using {name}, version {version}");
println!("{message}");
println!("{} is version {:.1}", name, version); // positional + precision
```

`{:.1}` formats a float to one decimal place; format specifiers also support width, padding, and alignment (e.g. `{:>10}` right-pads to width 10).

---

## 11.3 Common String Methods

```rust
let s = String::from("  Hello, Rust!  ");

let trimmed = s.trim();               // "Hello, Rust!"
let upper = trimmed.to_uppercase();   // "HELLO, RUST!"
let contains = trimmed.contains("Rust"); // true
let parts: Vec<&str> = trimmed.split(", ").collect(); // ["Hello", "Rust!"]
```

`String` also supports concatenation via `push_str` (append a `&str`), `push` (append a single `char`), and the `+` operator:

```rust
let mut greeting = String::from("Hello");
greeting.push_str(", world");
greeting.push('!');

let combined = greeting + " Bye!"; // + takes ownership of the left side
```

---

## 11.4 Slicing Strings Safely

Because Rust strings are UTF-8, indexing directly by byte position (`s[0]`) isn't allowed — a byte boundary might fall in the middle of a multi-byte character. Instead, slice by byte range, or iterate characters explicitly:

```rust
let s = String::from("hello");
let hi = &s[0..2]; // "he" — valid because these are single-byte ASCII chars

for c in s.chars() {
    print!("{c}-");
}
```

Slicing at a boundary that isn't a valid character edge will panic at runtime, so slice carefully with non-ASCII text.

[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[12]-Comments-and-Documentation.md)
