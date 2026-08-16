[Previous](./[13]-Stack-vs-Heap.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[15]-RAII.md)

*Memory Management*

# Lesson 14 - Dynamic Memory

## 14.1 Allocating With new

The `new` operator allocates memory on the heap and returns a pointer to it:

```cpp
int* p = new int;       // allocates one uninitialized int
int* q = new int(42);   // allocates and initializes to 42

MyClass* obj = new MyClass(); // allocates and constructs an object
```

Unlike stack variables, this memory persists until you explicitly release it — it does not disappear when the enclosing scope ends.

---

## 14.2 Freeing With delete

Every successful `new` must eventually be paired with a matching `delete`, or the memory is never reclaimed:

```cpp
int* p = new int(42);
std::cout << *p;
delete p;    // frees the memory
p = nullptr; // good practice: avoid leaving a dangling pointer around
```

After `delete`, the pointer still holds the old address but the memory is no longer valid — using it (a **dangling pointer**) is undefined behavior. Setting the pointer to `nullptr` after deleting it helps catch accidental reuse, since dereferencing `nullptr` fails predictably.

---

## 14.3 Arrays On The Heap

Allocating an array uses `new[]`, and must be freed with the matching `delete[]`:

```cpp
int* arr = new int[10]; // 10 ints on the heap

for (int i = 0; i < 10; i++) {
    arr[i] = i * i;
}

delete[] arr; // note the [], required for array allocations
```

Mixing `new`/`delete` with `new[]`/`delete[]` (using the wrong form) is undefined behavior — always match them correctly.

---

## 14.4 Common Pitfalls (Leaks, Dangling Pointers, Double Free)

Manual memory management is a frequent source of bugs:

```cpp
// Memory leak: allocated but never freed
void leaky() {
    int* p = new int(5);
} // p goes out of scope, but the memory it pointed to is never freed

// Dangling pointer: using memory after it's freed
int* p = new int(5);
delete p;
std::cout << *p; // undefined behavior

// Double free: freeing the same memory twice
int* q = new int(5);
delete q;
delete q; // undefined behavior
```

These bugs motivate the tools covered in the next two lessons: **RAII**, a pattern that ties resource cleanup to object lifetime automatically, and **smart pointers**, which apply RAII specifically to dynamic memory so you rarely need to call `delete` by hand.

[Previous](./[13]-Stack-vs-Heap.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[15]-RAII.md)
