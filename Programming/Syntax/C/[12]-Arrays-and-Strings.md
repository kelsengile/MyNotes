[Previous](./[11]-Pointers-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[13]-Dynamic-Memory.md)

*Pointers & Memory*

# Lesson 12 - Arrays And Strings

## 12.1 Declaring and Using Arrays

An array is a fixed-size, contiguous block of elements of the same type:

```c
int scores[5] = {90, 85, 77, 92, 68};

printf("%d\n", scores[0]);   // 90 -- indexing starts at 0
printf("%d\n", scores[4]);   // 68 -- last element

scores[2] = 100;             // modify an element
```

C does **not** check array bounds at runtime. Accessing `scores[5]` or beyond reads or writes memory outside the array — undefined behavior that often corrupts nearby data without any immediate error (see Lesson 15.3 on buffer overflows).

```c
int size = sizeof(scores) / sizeof(scores[0]);   // number of elements: 5
```

---

## 12.2 Arrays and Pointers

An array's name, in most expressions, **decays** into a pointer to its first element:

```c
int arr[3] = {1, 2, 3};
int *p = arr;          // no & needed -- arr already decays to &arr[0]

printf("%d\n", arr[1]);    // 2
printf("%d\n", *(p + 1));  // 2 -- equivalent, using pointer arithmetic
```

In fact, `arr[i]` is defined in the language as shorthand for `*(arr + i)` — array indexing and pointer arithmetic are two syntaxes for the same operation. This is why array parameters in function signatures are actually treated as pointers:

```c
void print_all(int *arr, int size) {   // equivalent to int arr[]
    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
}
```

Note that a function receiving an array parameter cannot compute its size with `sizeof` — the size information is lost once it decays to a pointer, which is why `size` must be passed explicitly.

---

## 12.3 Strings as Char Arrays

C has no dedicated string type — a string is simply an array of `char`:

```c
char name[6] = {'H', 'e', 'l', 'l', 'o', '\0'};

// or, more conveniently, using a string literal:
char name2[] = "Hello";   // compiler adds the '\0' automatically
```

String literals like `"Hello"` are stored as `char` arrays with an automatically-appended null terminator.

---

## 12.4 Null Termination

C strings are **null-terminated**: a special `'\0'` byte marks where the string ends. Functions like `printf("%s", ...)` read characters until they hit this terminator:

```c
char greeting[] = "Hi";
// memory layout: 'H' 'i' '\0'
//                 0    1   2   (indices)

printf("%s\n", greeting);   // "Hi" -- stops at '\0'
```

Forgetting the null terminator — for example, when building a string manually — causes functions to read past the intended end of the array, into whatever memory happens to follow:

```c
char broken[2] = {'H', 'i'};   // no room for '\0'!
printf("%s\n", broken);         // undefined behavior: reads out of bounds
```

Always size character arrays with room for the terminator: a string of `n` visible characters needs an array of at least `n + 1` bytes.

---

## 12.5 The string.h Library

The standard library provides common string operations in `<string.h>`:

```c
#include <string.h>

char a[20] = "Hello";
char b[] = "World";

strlen(a);          // 5 -- length, not counting '\0'
strcpy(a, b);        // copies b into a: a becomes "World"
strcat(a, "!");       // appends: a becomes "World!"
strcmp(a, b);         // compares strings; 0 means equal
```

`strcpy` and `strcat` don't check whether the destination buffer is large enough to hold the result — writing past the end causes a buffer overflow. Where available, prefer the bounds-checked variants `strncpy` and `strncat`, which take a maximum length:

```c
strncpy(a, b, sizeof(a) - 1);
a[sizeof(a) - 1] = '\0';   // strncpy doesn't guarantee null-termination
```

Lesson 27 covers the `string.h` library in much greater depth.

---

[Previous](./[11]-Pointers-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[13]-Dynamic-Memory.md)
