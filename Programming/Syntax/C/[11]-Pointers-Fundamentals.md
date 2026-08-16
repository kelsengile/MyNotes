[Previous](./[10]-The-Preprocessor.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[12]-Arrays-and-Strings.md)

*Pointers & Memory*

# Lesson 11 - Pointers Fundamentals

## 11.1 What Is a Pointer?

Every variable lives somewhere in memory, at a numeric **address**. A pointer is a variable that stores an address instead of an ordinary value — it "points to" the location of another variable.

```c
int score = 42;
int *score_ptr = &score;   // score_ptr holds the address of score
```

The `*` in the declaration marks `score_ptr` as a pointer to an `int`. Pointers are typed — `int *` points to an `int`, `double *` points to a `double`, and so on — because the compiler needs to know how many bytes to read or write at that address.

---

## 11.2 The Address-Of Operator

`&` retrieves the memory address of a variable:

```c
int x = 10;
printf("Value: %d\n", x);
printf("Address: %p\n", (void *)&x);   // use %p for addresses, cast to void*
```

Every variable has an address, and printing it (in hexadecimal, like `0x7ffee3a1c9ac`) reveals where in memory it's stored. That address will differ between runs of the same program — the operating system typically randomizes stack and heap locations for security.

---

## 11.3 Dereferencing a Pointer

`*` also **dereferences** a pointer — accessing the value stored at the address it holds:

```c
int score = 42;
int *ptr = &score;

printf("%d\n", *ptr);   // 42 -- the value score points to

*ptr = 100;              // modifies score through the pointer
printf("%d\n", score);   // 100
```

This is how Lesson 9.3's `increment(&n)` example actually worked — the function received an address, and dereferenced it to modify the original variable.

---

## 11.4 Pointer Arithmetic

Adding to a pointer moves it forward by that many **elements**, not bytes — the compiler automatically scales by the pointed-to type's size:

```c
int arr[5] = {10, 20, 30, 40, 50};
int *p = arr;             // arr decays to a pointer to its first element

printf("%d\n", *p);       // 10
printf("%d\n", *(p + 1)); // 20 -- moves forward by sizeof(int) bytes
printf("%d\n", *(p + 2)); // 30
```

This relationship between pointers and arrays is explored fully in Lesson 12. Pointer arithmetic is only well-defined within (or one past the end of) a single array — walking a pointer outside those bounds is undefined behavior, one of the memory bugs covered in Lesson 15.

---

## 11.5 Null Pointers and Common Mistakes

A pointer that doesn't point to anything valid should be set to `NULL`:

```c
int *ptr = NULL;

if (ptr != NULL) {
    printf("%d\n", *ptr);   // only dereference after checking
} else {
    printf("ptr is not pointing to anything\n");
}
```

Dereferencing a `NULL` or uninitialized pointer is one of the most common C bugs, and typically crashes the program immediately (a **segmentation fault**):

```c
int *bad;             // uninitialized -- points to garbage
printf("%d\n", *bad); // undefined behavior, likely crash

int *also_bad = NULL;
printf("%d\n", *also_bad);  // definitely crashes: dereferencing NULL
```

Always initialize pointers, and always check for `NULL` before dereferencing a pointer that might not point to valid memory.

---

[Previous](./[10]-The-Preprocessor.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[12]-Arrays-and-Strings.md)
