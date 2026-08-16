[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[14]-Dynamic-Memory.md)

*Memory Management*

# Lesson 13 - Stack Vs Heap

## 13.1 Program Memory Layout

When a C++ program runs, its memory is typically divided into several regions:

- **Code (text) segment** — the compiled instructions themselves
- **Static/global segment** — global and `static` variables, living for the program's whole run
- **Stack** — local variables and function call information
- **Heap** — dynamically allocated memory, managed manually or via smart pointers

Understanding the stack and heap in particular is essential, since they behave very differently and are chosen deliberately depending on what you need.

---

## 13.2 The Stack

The **stack** stores local variables and manages function calls. Each function call pushes a new **stack frame** containing its parameters and local variables; the frame is popped automatically when the function returns.

```cpp
void doWork() {
    int x = 10; // allocated on the stack
    int y = 20; // also on the stack
} // x and y are automatically destroyed here
```

Stack allocation is extremely fast — it's just moving a pointer — and cleanup is automatic. The trade-off is that stack memory is limited in size, and anything allocated there disappears the moment its scope ends.

---

## 13.3 The Heap

The **heap** is a much larger pool of memory that you allocate and free manually (or through smart pointers), and it isn't tied to any particular function's scope:

```cpp
int* p = new int(42); // allocated on the heap
// p is still valid even after the function that created it returns,
// as long as you keep the pointer around
delete p; // must be freed manually
```

Heap allocation is slower than stack allocation and requires explicit management, but it's necessary when data needs to outlive the function that created it, or when its size isn't known until runtime.

---

## 13.4 Stack Vs Heap Trade-Offs

| | Stack | Heap |
|---|---|---|
| Speed | Very fast | Slower |
| Size | Limited (often a few MB) | Large (limited by system memory) |
| Lifetime | Tied to scope | Manual, until freed |
| Management | Automatic | Manual (or via smart pointers) |
| Risk | Stack overflow if too deep/large | Leaks, dangling pointers if mismanaged |

A useful default: prefer the stack whenever possible. Reach for the heap only when you need data to outlive its creating scope, need a size determined at runtime, or are working with large objects better allocated once. The next lesson covers how heap allocation actually works with `new` and `delete`.

[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[14]-Dynamic-Memory.md)
