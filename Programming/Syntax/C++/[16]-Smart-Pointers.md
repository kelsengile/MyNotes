[Previous](./[15]-RAII.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[17]-Move-Semantics.md)

*Memory Management*

# Lesson 16 - Smart Pointers

## 16.1 Why Smart Pointers

Smart pointers, from `<memory>`, apply RAII specifically to heap-allocated objects: they wrap a raw pointer and automatically call `delete` when they go out of scope, eliminating the manual bookkeeping and most causes of leaks and dangling pointers. Modern C++ code rarely calls `new`/`delete` directly — it uses smart pointers instead.

---

## 16.2 unique_ptr

`std::unique_ptr` owns its object **exclusively** — no two `unique_ptr`s can own the same object at once. It cannot be copied, only **moved**:

```cpp
#include <memory>

std::unique_ptr<int> p = std::make_unique<int>(42);
std::cout << *p; // 42

std::unique_ptr<int> q = std::move(p); // ownership transfers to q
// p is now empty (nullptr); using *p would be undefined behavior
```

Use `unique_ptr` as the default choice whenever an object has a single, clear owner — which is most of the time.

---

## 16.3 shared_ptr

`std::shared_ptr` allows **multiple** owners of the same object, tracked through an internal reference count. The object is destroyed automatically once the last `shared_ptr` referring to it goes out of scope:

```cpp
#include <memory>

std::shared_ptr<int> a = std::make_shared<int>(42);
std::shared_ptr<int> b = a; // both a and b now own the same int; count is 2

std::cout << a.use_count(); // 2
// the int is freed only when both a and b have gone out of scope
```

`shared_ptr` has more overhead than `unique_ptr`, due to the reference counting, so use it only when shared ownership is genuinely needed.

---

## 16.4 weak_ptr

`std::weak_ptr` observes an object owned by a `shared_ptr` **without** contributing to its reference count. This is essential for breaking reference cycles, where two objects hold `shared_ptr`s to each other and would otherwise never be freed:

```cpp
#include <memory>

std::shared_ptr<int> shared = std::make_shared<int>(42);
std::weak_ptr<int> weak = shared; // doesn't increase the reference count

if (auto locked = weak.lock()) { // returns a shared_ptr if the object still exists
    std::cout << *locked;
} else {
    std::cout << "Object no longer exists\n";
}
```

---

## 16.5 Choosing The Right Smart Pointer

| Situation | Use |
|---|---|
| Single, clear owner | `unique_ptr` |
| Multiple objects share ownership | `shared_ptr` |
| Need to observe without owning (avoid cycles) | `weak_ptr` |
| Object doesn't outlive its scope, and isn't heap-allocated at all | Plain stack variable — no smart pointer needed |

A good rule of thumb: default to `unique_ptr`, reach for `shared_ptr` only when ownership is genuinely shared, and avoid raw `new`/`delete` in application code entirely.

[Previous](./[15]-RAII.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[17]-Move-Semantics.md)
