# Memory Management

Memory management is how a program acquires, uses, and releases the memory it needs to run. Understanding it helps explain program performance, why certain bugs occur, and why languages differ in how they handle variables, objects, and data lifetimes.

---

## 9.1 Stack vs. Heap

Most programs divide memory into two main regions: the **stack** and the **heap**. They differ in how memory is allocated, how long it lives, and how fast it is to use.

### 9.1.1 The Stack

The stack is a region of memory that stores data in a strict **Last In, First Out (LIFO)** order. Each time a function is called, a new **stack frame** is pushed containing its local variables, parameters, and return address; when the function returns, that frame is popped and its memory is automatically reclaimed.

```c
// C
void doWork() {
    int x = 10;        // allocated on the stack
    int y = 20;         // allocated on the stack
    int sum = x + y;    // also on the stack
}   // x, y, sum are automatically freed here, when doWork() returns
```

**Characteristics:**
- Very fast allocation/deallocation — just moving a stack pointer up or down.
- Size is fixed and limited (often a few MB); deeply nested or infinite recursion causes a **stack overflow**.
- Memory is automatically managed — no manual cleanup needed.
- Variables have a lifetime tied directly to the function call that created them.

### 9.1.2 The Heap

The heap is a larger, more flexible pool of memory used for data whose size or lifetime isn't known at compile time, or that needs to outlive the function that created it.

```c
// C
int* createArray(int size) {
    int* arr = malloc(size * sizeof(int));  // allocated on the heap
    return arr;  // arr's memory survives after the function returns
}
```

```python
# Python — objects like lists, dicts, and custom class instances live on the heap;
# only references to them live on the stack
def create_list():
    my_list = [1, 2, 3]  # the list itself lives on the heap
    return my_list          # returning it keeps it alive after the function ends
```

**Characteristics:**
- Slower to allocate/deallocate than the stack, since the memory allocator must find/track free blocks.
- Size is much larger and more flexible, limited mainly by available system memory.
- Lifetime is independent of any particular function call — memory persists until it is explicitly freed (manual memory management) or becomes unreachable and is collected (garbage collection).
- Requires some mechanism — manual (`free`/`delete`) or automatic (garbage collector) — to reclaim memory when it's no longer needed.

### 9.1.3 Stack vs. Heap Comparison

| Aspect                | Stack                              | Heap                                    |
|--------------------------|----------------------------------------|-----------------------------------------|
| Allocation speed            | Very fast (pointer bump)          | Slower (allocator bookkeeping)          |
| Size                          | Small, fixed, limited            | Large, flexible                         |
| Lifetime                        | Tied to function call scope    | Until freed / garbage collected         |
| Managed by                        | Automatic (compiler/runtime) | Manual or garbage collector             |
| Typical contents                    | Local variables, primitives, function call info | Objects, dynamically sized data, long-lived data |
| Fragmentation risk                     | None                       | Possible over time                      |

### 9.1.4 What Goes Where

In many languages, primitive/value types (numbers, booleans, fixed-size structs) tend to live on the stack when declared as local variables, while objects, arrays, and anything created with a dynamic allocator (`new`, `malloc`, class instantiation) live on the heap — with only a reference or pointer to that heap memory stored on the stack.

```java
// Java
void example() {
    int x = 5;                     // primitive: on the stack
    Person p = new Person("Ana");  // 'p' (the reference) is on the stack;
                                    // the actual Person object is on the heap
}
```

---

## 9.2 Pointers & References

A **pointer** (or reference) is a variable that stores the memory address of another value, rather than the value itself. Pointers/references are what allow heap-allocated data to be accessed, passed around, and shared.

### 9.2.1 Pointers in Low-Level Languages (C/C++)

```c
// C
int x = 10;
int* p = &x;   // p holds the address of x ('&' = address-of)

printf("%d\n", x);    // 10
printf("%d\n", *p);   // 10 — '*' dereferences the pointer to get the value
*p = 20;                // modifies x indirectly through the pointer
printf("%d\n", x);    // 20
```

**Key operators (C/C++):**
- `&variable` — get the address of a variable.
- `*pointer` — dereference a pointer to access/modify the value it points to.
- `pointer->field` — dereference a pointer to a struct/object and access a field (C++).

### 9.2.2 Pointer Arithmetic

In C/C++, pointers can be incremented or offset to move through contiguous memory, which is how array indexing is implemented under the hood.

```c
int arr[3] = {10, 20, 30};
int* p = arr;        // points to arr[0]
printf("%d\n", *p);      // 10
p++;                        // move to next int (adds sizeof(int) bytes)
printf("%d\n", *p);      // 20
printf("%d\n", *(p + 1)); // 30
```

### 9.2.3 Null / Invalid Pointers

A pointer that doesn't point to a valid piece of memory is called a **null pointer** (explicitly set to "point to nothing") or may simply be uninitialized/invalid. Dereferencing either is a common source of crashes.

```c
int* p = NULL;
printf("%d\n", *p);  // undefined behavior / crash — dereferencing a null pointer
```

### 9.2.4 References in Higher-Level Languages

Languages like Python, Java, and JavaScript don't expose raw pointers, but variables referring to objects still work by **reference** under the hood — a variable holds a reference to an object's location, not the object's data directly.

```python
a = [1, 2, 3]
b = a          # b references the SAME list object as a
b.append(4)
print(a)       # [1, 2, 3, 4] — a is affected too, since a and b point to the same object

c = a[:]       # a copy — creates a new list object
c.append(5)
print(a)       # [1, 2, 3, 4] — unaffected by changes to c
```

```javascript
let obj1 = { value: 1 };
let obj2 = obj1;          // obj2 references the same object
obj2.value = 99;
console.log(obj1.value);  // 99 — same underlying object
```

### 9.2.5 References vs. Raw Pointers

| Aspect                     | Raw Pointer (C/C++)                | Reference (Java/Python/JS, etc.)      |
|-------------------------------|----------------------------------------|--------------------------------------|
| Arithmetic                       | Allowed                            | Not allowed                          |
| Can be null/invalid                | Yes                              | Yes (`null`/`None`/`undefined`), but not "dangling" the same way |
| Manual memory control                | Full control                   | Managed automatically (usually GC)   |
| Safety                                 | Unsafe if misused            | Generally safer, bounds/type checked |

### 9.2.6 References/Borrowing in Modern Systems Languages

Rust formalizes references with compile-time **ownership and borrowing** rules to prevent common pointer bugs without needing a garbage collector.

```rust
fn print_length(s: &String) {  // borrows a reference, doesn't take ownership
    println!("{}", s.len());
}

let text = String::from("hello");
print_length(&text);   // pass a reference
println!("{}", text);  // still valid here — ownership wasn't moved
```

---

## 9.3 Manual vs. Garbage-Collected Memory

Once heap memory is allocated, something has to decide when it's safe to reclaim it. Languages take one of two broad approaches.

### 9.3.1 Manual Memory Management

The programmer is responsible for explicitly allocating and freeing heap memory.

```c
// C
int* numbers = malloc(10 * sizeof(int));  // allocate
// ... use numbers ...
free(numbers);                                // must manually release it
numbers = NULL;                                 // good practice: avoid a dangling pointer
```

```cpp
// C++
int* value = new int(42);   // allocate
delete value;                     // must manually release it
value = nullptr;
```

**Trade-offs:**
- Gives precise, predictable control over when memory is allocated and freed — important for performance-critical or resource-constrained systems (embedded devices, game engines, operating systems).
- Places the full burden of correctness on the programmer — forgetting to free memory or freeing it incorrectly causes bugs (see 9.4).

### 9.3.2 Garbage-Collected Memory

A **garbage collector (GC)** automatically tracks which heap objects are still reachable (accessible) from the program and periodically reclaims memory for objects that are no longer reachable.

```python
# Python — memory for these objects is reclaimed automatically once unreachable
def create_data():
    data = [1, 2, 3, 4, 5]
    return data

result = create_data()
# result stays alive as long as it's referenced;
# once nothing references it, Python's garbage collector frees it
```

```java
// Java
Person p = new Person("Ana");
p = null;   // the Person object becomes unreachable and eligible for garbage collection
```

**Common GC strategies (conceptual, not something most application code interacts with directly):**
- **Reference counting:** each object tracks how many references point to it; when the count hits zero, it's freed immediately (used partly in Python, and fully in Swift's ARC). Struggles with **reference cycles** (two objects referencing each other) unless paired with a cycle detector.
- **Tracing / mark-and-sweep:** periodically starts from a set of "roots" (global variables, active stack frames) and marks everything reachable; anything not marked is considered garbage and swept away (used in Java, JavaScript engines, Go).
- **Generational GC:** an optimization that separates short-lived ("young") objects from long-lived ("old") ones, since most objects die young — collecting the young generation frequently is cheaper than scanning everything each time.

**Trade-offs:**
- Removes an entire class of bugs (leaks from forgetting to free, dangling pointers from freeing too early) and greatly reduces developer burden.
- Introduces some performance overhead and occasional, sometimes unpredictable pauses ("GC pauses") while collection runs, which can matter for latency-sensitive applications.

### 9.3.3 Comparison

| Aspect                    | Manual (C/C++)                    | Garbage-Collected (Python, Java, JS, Go, etc.) |
|-------------------------------|----------------------------------------|-------------------------------------------------|
| Who frees memory                 | Programmer, explicitly           | Runtime, automatically                          |
| Performance predictability          | High (no GC pauses)          | Can have pauses/overhead                        |
| Risk of leaks/dangling pointers      | Higher, if misused          | Much lower (though leaks are still possible via lingering references) |
| Developer effort                       | Higher                    | Lower                                           |
| Typical use cases                        | Systems programming, embedded, performance-critical code | General application development |

### 9.3.4 Hybrid / Modern Approaches

Some languages take a middle path:
- **Rust** uses compile-time ownership and borrowing rules (no garbage collector, no manual `free` calls needed) — memory is freed deterministically when its owning variable goes out of scope.
- **Swift/Objective-C** use **Automatic Reference Counting (ARC)**, inserting retain/release calls automatically at compile time rather than running a background collector.
- **C++** encourages **RAII** (Resource Acquisition Is Initialization) and smart pointers (`std::unique_ptr`, `std::shared_ptr`) to tie manual allocation to object lifetimes automatically.

```cpp
// C++ smart pointer — memory is freed automatically when 'ptr' goes out of scope
#include <memory>
std::unique_ptr<int> ptr = std::make_unique<int>(42);
// no explicit delete needed
```

---

## 9.4 Common Memory Bugs (Leaks, Dangling Pointers)

### 9.4.1 Memory Leaks

A **memory leak** occurs when a program allocates memory but never releases it, even though it's no longer needed — the memory becomes unreachable to reclaim (in manual management) or is kept artificially "alive" through lingering references (in garbage-collected languages), gradually consuming more memory over time.

```c
// C — classic leak: allocated memory is never freed
void leak() {
    int* data = malloc(100 * sizeof(int));
    // ... use data ...
    // missing: free(data);
}   // 'data' pointer is gone, but the memory it pointed to is now unreachable and unrecoverable
```

```javascript
// JavaScript — a leak can still happen via an ever-growing reference,
// even with garbage collection
let cache = [];
function addToCache(item) {
    cache.push(item);  // if items are added but never removed,
}                          // 'cache' grows indefinitely and is never collected
```

**Common causes:**
- Forgetting to `free`/`delete` allocated memory (manual management).
- Holding references longer than needed — global caches, event listeners, or closures that are never cleared (garbage-collected languages).
- Circular references in systems relying purely on reference counting without cycle detection.

### 9.4.2 Dangling Pointers

A **dangling pointer** is a pointer that still holds the address of memory that has already been freed (or otherwise no longer valid). Using it leads to **undefined behavior** — it might crash, silently corrupt data, or appear to "work" until it doesn't.

```c
// C
int* p = malloc(sizeof(int));
*p = 42;
free(p);          // memory is released...
printf("%d\n", *p);  // ...but p still points there — dangling pointer, undefined behavior
```

**Common causes and mitigations:**
- Using a pointer after `free`/`delete` — mitigate by setting the pointer to `NULL`/`nullptr` immediately after freeing.
- Returning a pointer/reference to a local (stack-allocated) variable, which is destroyed once the function returns.

```c
// C — returns a dangling pointer to a destroyed stack variable
int* badFunction() {
    int localVar = 42;
    return &localVar;   // localVar's memory is invalid once the function returns
}
```

### 9.4.3 Double Free

Calling `free` (or `delete`) on the same memory more than once, which can corrupt the memory allocator's internal bookkeeping and cause crashes or security vulnerabilities.

```c
int* p = malloc(sizeof(int));
free(p);
free(p);   // double free — undefined behavior
```

**Mitigation:** set pointers to `NULL` after freeing (freeing `NULL` is safe/a no-op in C), and be careful with shared ownership of the same pointer across multiple parts of a program.

### 9.4.4 Buffer Overflows / Out-of-Bounds Access

Writing or reading past the boundary of allocated memory (e.g., an array or buffer), which can corrupt adjacent memory or expose security vulnerabilities.

```c
int arr[5];
arr[5] = 10;   // out of bounds — valid indices are 0-4; undefined behavior
```

### 9.4.5 Use of Uninitialized Memory

Reading a variable or heap block before it has been assigned a value, which can produce unpredictable results since the memory may contain leftover data from a previous use.

```c
int x;                    // uninitialized
printf("%d\n", x);   // unpredictable value — whatever was previously in that memory
```

### 9.4.6 How Garbage-Collected Languages Avoid Most of These

Since garbage-collected languages don't expose manual `free`/`delete`, dangling pointers and double frees essentially cannot occur through normal application code — the runtime guarantees memory isn't reclaimed while still reachable. However, **memory leaks are still possible**, just through a different mechanism: unintentionally keeping references alive (e.g., forgotten event listeners, ever-growing caches, or closures capturing large objects longer than intended).

### 9.4.7 Tools for Detecting Memory Bugs

Common tools used to catch these issues during development:
- **Valgrind** — detects leaks, invalid memory access, and use of uninitialized memory in C/C++ programs.
- **AddressSanitizer (ASan)** — a compiler-based tool that detects out-of-bounds access, use-after-free, and related bugs at runtime.
- **Static analyzers / linters** — catch some classes of memory bugs before the program even runs.
- Language-level safety features (e.g., Rust's borrow checker) that prevent entire categories of these bugs at compile time.

### 9.4.8 Best Practices

- Pair every allocation with a clear, predictable point of deallocation (or use RAII/smart pointers to make this automatic).
- Set pointers to `NULL`/`nullptr` after freeing them.
- Avoid returning pointers/references to local (stack) variables.
- Prefer higher-level, safer abstractions (smart pointers, containers, garbage-collected languages) unless manual control is specifically required.
- In garbage-collected languages, be mindful of long-lived references (global state, caches, subscriptions/listeners) that can prevent otherwise-unused objects from being collected.