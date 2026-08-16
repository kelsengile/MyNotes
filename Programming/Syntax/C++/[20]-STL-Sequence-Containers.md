[Previous](./[19]-std-string.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[21]-STL-Associative-Containers.md)

*Data Structures*

# Lesson 20 - STL Sequence Containers

## 20.1 std::vector

`std::vector` is a dynamically-sized array — the most commonly used container in C++. It grows automatically as elements are added:

```cpp
#include <vector>

std::vector<int> numbers = {1, 2, 3};

numbers.push_back(4);      // {1, 2, 3, 4}
numbers.pop_back();        // {1, 2, 3}
std::cout << numbers[0];   // 1, no bounds checking
std::cout << numbers.at(0); // 1, throws std::out_of_range if invalid
std::cout << numbers.size(); // 3

for (int n : numbers) {
    std::cout << n << " ";
}
```

Internally, a `vector` stores its elements contiguously, like a C-style array, and reallocates to a larger buffer (typically doubling in size) when it runs out of room. This makes it fast for appending at the end and for random access, but slower for inserting or removing from the middle.

---

## 20.2 std::array

`std::array` is a fixed-size array that, unlike a raw C-style array, knows its own size and works well with STL algorithms:

```cpp
#include <array>

std::array<int, 3> fixed = {10, 20, 30};

std::cout << fixed.size();  // 3, always
std::cout << fixed[1];      // 20
```

Use `std::array` when the number of elements is known at compile time and won't change — it has no dynamic allocation overhead at all, unlike `vector`.

---

## 20.3 std::deque

`std::deque` ("double-ended queue") supports fast insertion and removal at **both** ends, unlike `vector`, which is only efficient at the end:

```cpp
#include <deque>

std::deque<int> d = {2, 3, 4};

d.push_front(1); // {1, 2, 3, 4}
d.push_back(5);  // {1, 2, 3, 4, 5}
d.pop_front();   // {2, 3, 4, 5}
```

Internally, a `deque` isn't stored as one contiguous block — it's typically implemented as a series of fixed-size chunks — which is what makes fast insertion at the front possible.

---

## 20.4 Choosing A Sequence Container

| Need | Container |
|---|---|
| General-purpose, grows dynamically | `std::vector` (default choice) |
| Fixed size known at compile time | `std::array` |
| Frequent insertion/removal at both ends | `std::deque` |
| Frequent insertion/removal in the middle | `std::list` (a doubly-linked list, less commonly needed) |

`std::vector` should be your default unless you have a specific reason to reach for something else — it has the best cache performance for most workloads due to storing elements contiguously in memory.

[Previous](./[19]-std-string.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[21]-STL-Associative-Containers.md)
