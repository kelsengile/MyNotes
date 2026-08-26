[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Functions And Scope

## 10.1 Defining Functions

Functions are declared with `fn`, using `snake_case` names by convention. Parameter types are required — Rust doesn't infer them:

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b   // no semicolon: this is the returned value
}

fn main() {
    let result = add(2, 3);
    println!("{result}");
}
```

Functions can appear in any order in a file — Rust doesn't require them to be declared before use, unlike variables.

---

## 10.2 Return Values

The return type is declared after `->`. A function returns the value of its final expression, or you can `return` early:

```rust
fn classify(n: i32) -> &'static str {
    if n < 0 {
        return "negative";
    }
    if n == 0 {
        return "zero";
    }
    "positive"
}
```

A function with no return type implicitly returns `()`, the unit type.

---

## 10.3 Scope and Blocks

Every `{}` block introduces a new scope. Variables declared inside a block are dropped (deallocated) when the block ends:

```rust
fn main() {
    let x = 5;
    {
        let y = 10;
        println!("{x} and {y}");
    } // y goes out of scope here
    println!("{x}"); // fine
    // println!("{y}"); // error: y is not in scope
}
```

Scope is central to how Rust manages memory safety — it's revisited heavily in the Ownership lessons (13–17).

---

## 10.4 Parameters vs. Arguments

The variables listed in a function's definition (`a: i32, b: i32`) are **parameters**; the actual values passed in when calling it (`add(2, 3)`) are **arguments**. Rust requires every parameter's type to be explicit, since function signatures form part of the compiler's guarantee about how a function can be used.

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[11]-String-Formatting.md)
