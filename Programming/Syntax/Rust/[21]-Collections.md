[Previous](./[20]-Match-Expression.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[22]-Option-and-Result.md)

*Data Structures*

# Lesson 21 - Collections: Vec, HashMap, HashSet, VecDeque

## 21.1 Vec: Growable Lists

`Vec<T>` is a heap-allocated, growable list of values of the same type — the most commonly used collection in Rust:

```rust
let mut numbers: Vec<i32> = Vec::new();
numbers.push(1);
numbers.push(2);
numbers.push(3);

let numbers2 = vec![1, 2, 3]; // macro shorthand with initial values

let second = &numbers[1];             // panics if out of bounds
let maybe_second = numbers.get(1);    // returns Option<&i32> instead

for n in &numbers {
    println!("{n}");
}
```

Prefer `.get()` over indexing (`[]`) when the index might be out of bounds, since `.get()` returns an `Option` instead of panicking.

---

## 21.2 HashMap: Key-Value Storage

`HashMap<K, V>` stores key-value pairs with no guaranteed order, giving average O(1) lookup by key:

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team = String::from("Blue");
let score = scores.get(&team); // Option<&i32>

scores.entry(String::from("Blue")).or_insert(0); // insert only if absent
```

Keys and values must each be a consistent type across the whole map, and keys must implement the `Eq` and `Hash` traits (covered in Lesson 29).

---

## 21.3 HashSet: Unique Values

`HashSet<T>` stores unique values with no associated data — effectively a `HashMap<T, ()>` — and is ideal for membership checks and de-duplication:

```rust
use std::collections::HashSet;

let mut visited = HashSet::new();
visited.insert("Paris");
visited.insert("Tokyo");
visited.insert("Paris"); // no-op: already present

println!("{}", visited.contains("Tokyo")); // true
println!("{}", visited.len());              // 2
```

---

## 21.4 VecDeque: Double-Ended Queue

`VecDeque<T>` supports efficient push/pop from **both** the front and back, unlike `Vec`, which is efficient at the back only:

```rust
use std::collections::VecDeque;

let mut queue: VecDeque<i32> = VecDeque::new();
queue.push_back(1);
queue.push_back(2);
queue.push_front(0);

while let Some(front) = queue.pop_front() {
    println!("{front}"); // 0, then 1, then 2
}
```

`VecDeque` is the natural choice when implementing queues, sliding windows, or any structure that needs efficient operations at both ends.

[Previous](./[20]-Match-Expression.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[22]-Option-and-Result.md)
