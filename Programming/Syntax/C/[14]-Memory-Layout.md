[Previous](./[13]-Dynamic-Memory.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[15]-Common-Memory-Bugs.md)

*Pointers & Memory*

# Lesson 14 - Memory Layout

## 14.1 The Stack

The **stack** is where local variables and function call information live. Every time a function is called, a new **stack frame** is pushed, holding its local variables, parameters, and return address; when the function returns, that frame is popped and its memory is instantly reclaimed.

```c
void example(void) {
    int x = 5;   // lives on the stack, in example's stack frame
}   // x is destroyed automatically here, when the frame is popped
```

The stack is fast (allocation is just moving a pointer) but limited in size and strictly nested — you can't keep a pointer to stack memory alive after its function returns:

```c
int *dangerous(void) {
    int local = 42;
    return &local;   // BUG: local's memory is reclaimed the moment the function returns
}
```

Deeply nested or infinite recursion (Lesson 9.5) exhausts the stack, causing a **stack overflow** crash.

---

## 14.2 The Heap

The **heap** is a much larger pool of memory that you manage manually via `malloc`, `calloc`, `realloc`, and `free` (Lesson 13). Unlike the stack, heap memory persists until you explicitly free it — which is exactly what makes it suitable for data that needs to outlive the function that created it, or whose size isn't known until runtime.

```c
int *make_array(int size) {
    int *arr = malloc(size * sizeof(int));   // lives on the heap
    return arr;   // safe: heap memory outlives this function
}
```

The tradeoff is that heap allocation is slower than stack allocation, and comes with manual bookkeeping responsibility that the stack handles automatically.

---

## 14.3 Data and BSS Segments

Global and `static` variables (Lesson 9.4) don't live on the stack or heap — they're placed in dedicated segments that exist for the entire life of the program:

- The **data segment** holds initialized global/static variables.
- The **BSS segment** ("Block Started by Symbol") holds global/static variables that are uninitialized, or explicitly initialized to zero — the OS zeroes this memory once at program startup, without the executable needing to store the zeros on disk.

```c
int initialized_global = 42;   // data segment
int uninitialized_global;      // BSS segment, starts at 0

void example(void) {
    static int counter = 0;    // BSS segment (starts at 0, retains value between calls)
}
```

---

## 14.4 The Text Segment

The **text segment** (also called the code segment) holds the compiled machine instructions themselves — the actual executable code. It's typically marked **read-only** by the operating system, which is why attempting to modify code, or writing through a corrupted function pointer, typically crashes the program rather than silently altering it.

```c
void say_hello(void) {
    printf("Hello\n");
}
// The compiled instructions for say_hello live in the text segment
```

---

## 14.5 Putting It All Together

A running program's address space, from low to high addresses, is typically laid out like this:

```
High addresses
┌────────────────────┐
│       Stack         │  <- grows downward; local variables, function calls
├────────────────────┤
│         ↓            │
│   (unused space)     │
│         ↑            │
├────────────────────┤
│        Heap          │  <- grows upward; malloc/calloc/realloc
├────────────────────┤
│  BSS (uninitialized) │  <- global/static, zero-initialized
├────────────────────┤
│  Data (initialized)  │  <- global/static, initialized
├────────────────────┤
│        Text           │  <- compiled instructions, read-only
└────────────────────┘
Low addresses
```

The stack and heap grow toward each other from opposite ends of the available space, which is why exhausting either one (deep recursion on the stack, or too many allocations on the heap) eventually causes a crash. Understanding this layout makes it much easier to reason about the memory bugs covered in the next lesson.

---

[Previous](./[13]-Dynamic-Memory.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[15]-Common-Memory-Bugs.md)
