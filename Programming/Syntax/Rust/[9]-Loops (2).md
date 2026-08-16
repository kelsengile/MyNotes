[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[10]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops: loop, while, for, break With Values

## 9.1 The loop Keyword

`loop` repeats a block forever until you explicitly `break` out of it:

```rust
let mut count = 0;

loop {
    count += 1;
    if count == 5 {
        break;
    }
}
```

---

## 9.2 break With a Value

Unlike most languages, Rust's `loop` is an expression — `break` can return a value from it, useful for retry-style logic:

```rust
let mut counter = 0;

let result = loop {
    counter += 1;
    if counter == 10 {
        break counter * 2;
    }
};

println!("{result}"); // 20
```

---

## 9.3 while Loops

`while` repeats as long as a condition stays true — no manual `break` needed for the common case:

```rust
let mut number = 3;

while number != 0 {
    println!("{number}!");
    number -= 1;
}
println!("Liftoff!");
```

---

## 9.4 for Loops and Iterators

`for` loops iterate over anything implementing `Iterator` (see Lesson 35), and are the idiomatic way to loop over collections or ranges:

```rust
let arr = [10, 20, 30];

for element in arr {
    println!("{element}");
}

for number in 1..4 {   // range, exclusive of 4
    println!("{number}");
}
```

`for` is generally preferred over `while` for iteration — it's harder to get an off-by-one error wrong, since there's no manual index or bounds check to manage.

---

## 9.5 Loop Labels

When loops are nested, labels (written `'label:`) let `break` or `continue` target a specific outer loop:

```rust
'outer: for x in 0..5 {
    for y in 0..5 {
        if x + y == 6 {
            break 'outer;
        }
    }
}
```

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[10]-Functions-and-Scope.md)
