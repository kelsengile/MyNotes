[Previous](./[20]-STL-Sequence-Containers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[22]-STL-Iterators-and-Algorithms.md)

*Data Structures*

# Lesson 21 - STL Associative Containers

## 21.1 std::map

`std::map` stores **key-value pairs**, automatically kept sorted by key (using `<` by default):

```cpp
#include <map>

std::map<std::string, int> ages;

ages["Alice"] = 30;
ages["Bob"] = 25;
ages.insert({"Carol", 35});

std::cout << ages["Alice"]; // 30

if (ages.find("Bob") != ages.end()) {
    std::cout << "Bob is in the map\n";
}

for (const auto& [name, age] : ages) { // iterates in sorted key order
    std::cout << name << ": " << age << "\n";
}
```

Note that `ages["Zeke"]` **inserts** a default-valued entry (`0` for `int`) if the key doesn't already exist — use `.find()` or `.count()` first if you just want to check for a key without creating it.

---

## 21.2 std::set

`std::set` stores a collection of **unique** values, also kept sorted:

```cpp
#include <set>

std::set<int> uniqueNumbers = {5, 1, 3, 1, 5}; // duplicates are dropped

// uniqueNumbers now contains {1, 3, 5}, in sorted order

uniqueNumbers.insert(2);   // {1, 2, 3, 5}
uniqueNumbers.erase(3);    // {1, 2, 5}

bool has1 = uniqueNumbers.count(1) > 0; // true
```

`std::set` is useful whenever you need to track membership or de-duplicate values while keeping them ordered.

---

## 21.3 std::unordered_map And std::unordered_set

`std::unordered_map` and `std::unordered_set` provide the same key-based semantics as `map`/`set`, but use a **hash table** internally instead of a sorted tree. This means no guaranteed ordering, but average **O(1)** lookup, insertion, and deletion instead of `map`/`set`'s **O(log n)**:

```cpp
#include <unordered_map>

std::unordered_map<std::string, int> scores;
scores["Alice"] = 90;
scores["Bob"] = 85;

// iteration order here is unspecified — don't rely on it
for (const auto& [name, score] : scores) {
    std::cout << name << ": " << score << "\n";
}
```

---

## 21.4 Choosing An Associative Container

| Need | Container |
|---|---|
| Key-value pairs, fastest lookup, order doesn't matter | `std::unordered_map` |
| Key-value pairs, need sorted iteration order | `std::map` |
| Unique values, fastest lookup, order doesn't matter | `std::unordered_set` |
| Unique values, need sorted iteration order | `std::set` |

Default to the `unordered_` versions for raw performance when order doesn't matter; reach for the sorted `map`/`set` when you specifically need elements in order, or need operations like finding the smallest key greater than some value.

[Previous](./[20]-STL-Sequence-Containers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[22]-STL-Iterators-and-Algorithms.md)
