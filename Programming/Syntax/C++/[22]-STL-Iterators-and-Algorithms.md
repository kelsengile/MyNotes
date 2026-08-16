[Previous](./[21]-STL-Associative-Containers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[23]-OOP-Classes-and-Objects.md)

*Data Structures*

# Lesson 22 - STL Iterators And Algorithms

## 22.1 What Is An Iterator

An **iterator** is an object that points to an element within a container and can be advanced to visit the next one — a generalized version of a pointer that works uniformly across very different container types:

```cpp
#include <vector>

std::vector<int> numbers = {10, 20, 30};

std::vector<int>::iterator it = numbers.begin(); // points to the first element
std::cout << *it;  // 10, dereference like a pointer

++it;               // advance to the next element
std::cout << *it;  // 20

std::vector<int>::iterator end = numbers.end(); // one PAST the last element
```

`begin()` and `end()` mark the range `[begin, end)` — note that `end()` doesn't point to a valid element, it's a marker for "one past the last," which is why comparing against it (`it != numbers.end()`) is the standard way to detect the end of a loop.

---

## 22.2 Iterator Categories

Different containers support different iterator capabilities:

- **Input/output iterators** — single-pass, forward-only
- **Forward iterators** — can be re-read, forward-only (e.g. `std::forward_list`)
- **Bidirectional iterators** — can move forward and backward (e.g. `std::map`, `std::list`)
- **Random access iterators** — can jump to any position in constant time, like pointer arithmetic (e.g. `std::vector`, `std::array`, `std::deque`)

You rarely need to worry about this hierarchy directly — it mainly affects which algorithms a given container can be used with, since some algorithms (like `std::sort`) require random access.

---

## 22.3 Common Algorithms (sort, find, count, Etc.)

The `<algorithm>` header provides generic functions that work on any container via iterators, rather than being tied to a specific container type:

```cpp
#include <algorithm>
#include <vector>

std::vector<int> nums = {5, 2, 8, 1, 9};

std::sort(nums.begin(), nums.end()); // {1, 2, 5, 8, 9}

auto it = std::find(nums.begin(), nums.end(), 8);
if (it != nums.end()) {
    std::cout << "Found 8\n";
}

int count = std::count(nums.begin(), nums.end(), 5); // how many 5s

int total = std::accumulate(nums.begin(), nums.end(), 0); // requires <numeric>

std::reverse(nums.begin(), nums.end());
```

Because these algorithms operate on iterator ranges rather than a specific container, the same `std::sort` call works on a `vector`, a `deque`, or a plain array — a key benefit of the STL's design.

---

## 22.4 Lambdas With Algorithms (Brief Preview)

Many algorithms accept a **predicate** — a small function describing custom logic — which is commonly written as a **lambda**, a topic covered in full detail later in **Lambda Expressions & Closures**:

```cpp
std::vector<int> nums = {1, 2, 3, 4, 5, 6};

int evenCount = std::count_if(nums.begin(), nums.end(),
    [](int n) { return n % 2 == 0; }); // counts even numbers: 3

std::sort(nums.begin(), nums.end(),
    [](int a, int b) { return a > b; }); // sort descending
```

This pairing of algorithms with lambdas is extremely common in modern C++ — it lets you customize standard operations without writing a full loop by hand.

[Previous](./[21]-STL-Associative-Containers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[23]-OOP-Classes-and-Objects.md)
