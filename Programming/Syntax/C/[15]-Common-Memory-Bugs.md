[Previous](./[14]-Memory-Layout.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[16]-Function-Pointers.md)

*Pointers & Memory*

# Lesson 15 - Common Memory Bugs

## 15.1 Memory Leaks

A **memory leak** happens when heap memory is allocated but never freed, and every reference to it is lost — the memory stays reserved for the rest of the program's run, unreachable and unusable.

```c
void leaky(void) {
    int *data = malloc(100 * sizeof(int));
    // ... use data ...
    // missing free(data)! this block is now unreachable and leaked
}
```

A single small leak might not matter much, but a leak inside a function called repeatedly (in a loop, or a long-running server) steadily consumes more and more memory until the program eventually runs out. Lesson 48 covers **Valgrind**, a tool that automatically detects exactly which allocations were never freed.

---

## 15.2 Dangling Pointers

A **dangling pointer** points to memory that has already been freed (or, for stack memory, has already gone out of scope). Using it reads or writes memory that may now belong to something else entirely.

```c
int *ptr = malloc(sizeof(int));
*ptr = 42;
free(ptr);

*ptr = 100;   // BUG: writing through a dangling pointer
```

As covered in Lesson 14.1, returning the address of a local variable creates the same problem:

```c
int *get_dangling(void) {
    int local = 10;
    return &local;   // local's stack memory is gone once this function returns
}
```

The fix, in both cases: don't use a pointer after the memory it refers to has been released, and set freed pointers to `NULL` so an accidental use crashes loudly instead of corrupting memory silently.

---

## 15.3 Buffer Overflows

A **buffer overflow** happens when you write past the bounds of an array or allocated block, corrupting whatever memory happens to sit next to it:

```c
char buffer[8];
strcpy(buffer, "This string is way too long for the buffer");
// writes far past the end of buffer -- undefined behavior, possibly a crash
// or, worse, silent corruption of other variables
```

Overflows are especially dangerous when they corrupt a stack frame's saved return address — a classic security vulnerability called **stack smashing**, which can let an attacker redirect program execution. Always ensure destination buffers are large enough, and prefer bounds-checked functions like `snprintf` and `strncpy` (Lesson 12.5, 27) over their unchecked counterparts.

---

## 15.4 Use-After-Free

**Use-after-free** is a specific, especially dangerous form of dangling-pointer bug: memory is freed, then reallocated for something else, and the old pointer is used to read or write it — silently corrupting whatever now occupies that memory.

```c
int *a = malloc(sizeof(int));
*a = 1;
free(a);

int *b = malloc(sizeof(int));   // may reuse the exact memory just freed from a
*b = 2;

printf("%d\n", *a);   // undefined: might print 2, garbage, or crash
```

Because the corrupted value often "looks" plausible, use-after-free bugs can go unnoticed for a long time and are a common source of security vulnerabilities. AddressSanitizer (Lesson 48) is specifically designed to catch these at the moment they happen, rather than letting them silently corrupt data.

---

## 15.5 Double Free

Calling `free` twice on the same pointer is a **double free** — undefined behavior that commonly corrupts the heap's internal bookkeeping structures, causing crashes or exploitable behavior much later in the program, far from where the actual bug is:

```c
int *ptr = malloc(sizeof(int));
free(ptr);
free(ptr);   // BUG: double free
```

The fix mirrors the dangling-pointer fix: set a pointer to `NULL` immediately after freeing it. Calling `free(NULL)` is explicitly defined by the C standard to do nothing safely, so this pattern eliminates the double-free risk entirely:

```c
int *ptr = malloc(sizeof(int));
free(ptr);
ptr = NULL;
free(ptr);   // safe no-op, because ptr is NULL
```

---

[Previous](./[14]-Memory-Layout.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[16]-Function-Pointers.md)
