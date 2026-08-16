[Previous](./[12]-Arrays-and-Strings.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[14]-Memory-Layout.md)

*Pointers & Memory*

# Lesson 13 - Dynamic Memory

## 13.1 Why Dynamic Memory?

Arrays declared like `int arr[10];` have a size fixed at compile time. Often you don't know how much memory you'll need until the program is running — for example, based on user input or a file's contents. **Dynamic memory allocation** requests memory from the **heap** at runtime, and gives you a pointer to it. The functions live in `<stdlib.h>`.

---

## 13.2 malloc and free

`malloc` (memory allocate) reserves a block of raw, uninitialized memory of a given size in bytes, and returns a pointer to it — or `NULL` if the allocation fails:

```c
#include <stdlib.h>

int *numbers = malloc(5 * sizeof(int));   // room for 5 ints

if (numbers == NULL) {
    // allocation failed -- handle the error, don't proceed
    return 1;
}

for (int i = 0; i < 5; i++) {
    numbers[i] = i * 10;
}

free(numbers);   // release the memory back to the system when done
numbers = NULL;   // avoid an accidental use of the now-invalid pointer
```

Every successful `malloc` must eventually be paired with exactly one `free`. Unlike local variables, heap memory is **not** automatically cleaned up when a function returns — it stays allocated until you explicitly free it, or the program ends.

---

## 13.3 calloc

`calloc` (clear allocate) also allocates memory, but takes the element count and size separately, and — unlike `malloc` — zero-initializes every byte:

```c
int *numbers = calloc(5, sizeof(int));   // 5 ints, all initialized to 0

if (numbers == NULL) {
    return 1;
}
// numbers[0] through numbers[4] are all guaranteed to be 0
free(numbers);
```

Use `calloc` when you need the memory to start zeroed out; use `malloc` when you're about to overwrite every byte anyway and don't need the (slightly slower) zeroing step.

---

## 13.4 realloc

`realloc` resizes a previously allocated block, preserving its existing contents up to the smaller of the old and new sizes:

```c
int *numbers = malloc(5 * sizeof(int));
// ... fill numbers ...

int *bigger = realloc(numbers, 10 * sizeof(int));
if (bigger == NULL) {
    // realloc failed -- the ORIGINAL 'numbers' pointer is still valid and unfreed
    free(numbers);
    return 1;
}
numbers = bigger;   // safe to reassign now that we know it succeeded
```

Never write `numbers = realloc(numbers, newSize);` directly — if `realloc` fails, it returns `NULL` and the original block is left untouched, but you've just overwritten your only pointer to it, leaking that memory permanently.

---

## 13.5 Avoiding Leaks and Dangling Pointers

Two rules prevent the majority of dynamic memory bugs:

1. **Every `malloc`/`calloc`/`realloc` needs exactly one matching `free`.** Forgetting it is a **memory leak** — the memory stays reserved for the rest of the program's life, even though nothing can reach it anymore.
2. **Never use a pointer after freeing it.** Once freed, the memory may be reused for something else; reading or writing through the old pointer is undefined behavior, known as a **dangling pointer**.

```c
int *ptr = malloc(sizeof(int));
*ptr = 5;
free(ptr);
printf("%d\n", *ptr);   // BUG: use-after-free, ptr is now dangling
```

Setting a pointer to `NULL` immediately after freeing it is a simple habit that turns a silent, hard-to-diagnose bug into an easy-to-spot `NULL` dereference. Lesson 15 covers these and other memory bugs — and the tools to catch them — in more detail.

---

[Previous](./[12]-Arrays-and-Strings.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[14]-Memory-Layout.md)
