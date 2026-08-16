[Previous](./[17]-Move-Semantics.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[19]-std-string.md)

*Data Structures*

# Lesson 18 - Arrays And C-Style Strings

## 18.1 Fixed-Size Arrays

A C-style array holds a fixed number of elements of the same type, allocated on the stack:

```cpp
int numbers[5] = {10, 20, 30, 40, 50};

std::cout << numbers[0]; // 10
numbers[2] = 99;          // modify the third element

int size = sizeof(numbers) / sizeof(numbers[0]); // 5
```

The size must be known at compile time (or via a runtime-sized `new[]`, covered in **Dynamic Memory**), and C++ does **not** check that an index is in bounds — accessing `numbers[10]` compiles but is undefined behavior.

---

## 18.2 Arrays And Pointer Decay

In most expressions, an array "decays" into a pointer to its first element:

```cpp
int numbers[3] = {1, 2, 3};
int* p = numbers; // decays to &numbers[0]

std::cout << *p;       // 1
std::cout << *(p + 1); // 2
std::cout << p[1];     // 2, same as above — [] works on pointers too
```

This is convenient, but it also means a function receiving an array parameter actually receives just a pointer, losing the size information:

```cpp
void printSize(int arr[]) {
    std::cout << sizeof(arr); // prints the size of a pointer, NOT the array!
}
```

Because of this pitfall, modern C++ generally prefers `std::array` or `std::vector` (covered in the next few lessons), which know their own size.

---

## 18.3 C-Style Strings

A C-style string is just a `char` array terminated by a null character (`'\0'`) marking its end:

```cpp
char greeting[] = "Hello"; // actually {'H','e','l','l','o','\0'}, 6 bytes

const char* literal = "Hello"; // a pointer to a read-only string literal
```

Functions from `<cstring>` operate on these, scanning until they hit the null terminator:

```cpp
#include <cstring>

char dest[20];
std::strcpy(dest, "Hello");           // copy
std::strcat(dest, ", World!");        // concatenate
int len = std::strlen(dest);          // length, not counting '\0'
bool same = std::strcmp(dest, "Hi") == 0; // 0 means equal
```

---

## 18.4 Common Pitfalls

C-style arrays and strings are a frequent source of bugs, mostly because C++ trusts the programmer completely and performs no automatic bounds checking:

```cpp
char buffer[5];
std::strcpy(buffer, "Hello World"); // buffer overflow: writes past the end, undefined behavior

int arr[3] = {1, 2, 3};
std::cout << arr[5]; // out-of-bounds read, undefined behavior — may not even crash
```

These risks are the main reason `std::string` and `std::vector`, covered next, are strongly preferred for everyday code — they manage their own memory safely and know their own size, while C-style arrays and strings remain important to understand for interfacing with C libraries and low-level code.

[Previous](./[17]-Move-Semantics.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[19]-std-string.md)
