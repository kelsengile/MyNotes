[Previous](./[15]-Common-Memory-Bugs.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[17]-Structs-and-Typedefs.md)

*Pointers & Memory*

# Lesson 16 - Function Pointers And Callbacks

## 16.1 What Is a Function Pointer?

Just as a regular pointer holds the address of a variable, a **function pointer** holds the address of a function — letting you store, pass, and call a function indirectly, chosen at runtime rather than hardcoded.

```c
int add(int a, int b) {
    return a + b;
}

int (*operation)(int, int);   // declares a pointer to a function taking (int, int), returning int
operation = add;               // points it at add (the & is optional here)

int result = operation(3, 4);  // calls add through the pointer: result = 7
```

The parentheses around `*operation` matter: `int (*operation)(int, int)` is a pointer to a function, while `int *operation(int, int)` would declare a function returning `int *` — a completely different thing.

---

## 16.2 Declaring and Using Function Pointers

Function pointer syntax reads more clearly once you name each part: the pointer's name, then its parameter types, then its return type.

```c
int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }

int (*math_op)(int, int) = add;
printf("%d\n", math_op(10, 3));   // 13 -- calls add(10, 3)

math_op = subtract;
printf("%d\n", math_op(10, 3));   // 7 -- same call site, now calls subtract(10, 3)
```

The pointer itself decides which function actually runs — the call site (`math_op(10, 3)`) doesn't change at all.

Because complex function pointer declarations get hard to read, a `typedef` (Lesson 17) is commonly used to give the type a clear name:

```c
typedef int (*MathOp)(int, int);

MathOp op = multiply;
printf("%d\n", op(4, 5));   // 20
```

---

## 16.3 Callbacks

A **callback** is a function passed as an argument to another function, to be called at some point inside it — a common pattern for customizing behavior without duplicating code.

```c
#include <stdio.h>

void for_each(int *arr, int size, void (*callback)(int)) {
    for (int i = 0; i < size; i++) {
        callback(arr[i]);   // call the passed-in function for each element
    }
}

void print_int(int x) {
    printf("%d\n", x);
}

void print_doubled(int x) {
    printf("%d\n", x * 2);
}

int main(void) {
    int numbers[] = {1, 2, 3};

    for_each(numbers, 3, print_int);       // prints 1 2 3
    for_each(numbers, 3, print_doubled);   // prints 2 4 6

    return 0;
}
```

`for_each` doesn't need to know what the callback actually does — it just needs to know its signature. This is exactly how the standard library's `qsort` lets you sort with a custom comparison function.

---

## 16.4 Arrays of Function Pointers

Function pointers can be stored in arrays, useful for dispatch tables that map an index or command to a specific behavior — avoiding a long `if`/`else` or `switch` chain:

```c
#include <stdio.h>

int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }
int multiply(int a, int b) { return a * b; }

int main(void) {
    int (*operations[3])(int, int) = {add, subtract, multiply};
    const char *names[3] = {"add", "subtract", "multiply"};

    for (int i = 0; i < 3; i++) {
        printf("%s(6, 2) = %d\n", names[i], operations[i](6, 2));
    }
    // add(6, 2) = 8
    // subtract(6, 2) = 4
    // multiply(6, 2) = 12

    return 0;
}
```

This pattern scales well — adding a new operation means adding one entry to the array, rather than another branch in a growing conditional chain.

---

[Previous](./[15]-Common-Memory-Bugs.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[17]-Structs-and-Typedefs.md)
