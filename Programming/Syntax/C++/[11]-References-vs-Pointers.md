[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[12]-Error-Handling.md)

*Core Syntax*

# Lesson 11 - References Vs Pointers

## 11.1 What Is A Pointer

A **pointer** is a variable that stores a memory address — the location of another variable:

```cpp
int value = 42;
int* ptr = &value; // ptr holds the address of value

std::cout << ptr;   // prints the memory address
std::cout << *ptr;  // dereference: prints 42, the value at that address

*ptr = 100; // changes value to 100, through the pointer
```

`&` gets the address of a variable; `*` (when used on a pointer) **dereferences** it, accessing the value it points to. A pointer that points to nothing should be set to `nullptr`:

```cpp
int* empty = nullptr;
```

---

## 11.2 What Is A Reference

A **reference** is an alias for an existing variable — another name for the same memory, not a separate object:

```cpp
int value = 42;
int& ref = value; // ref is now another name for value

ref = 100; // this also changes value, since they're the same object
std::cout << value; // prints 100
```

Unlike a pointer, a reference **must** be initialized when declared, and can never be reassigned to refer to something else afterward.

---

## 11.3 Pointers Vs References: Key Differences

| | Pointer | Reference |
|---|---|---|
| Can be null | Yes (`nullptr`) | No |
| Can be reassigned | Yes | No |
| Needs `*` to access value | Yes | No, used like a normal variable |
| Can be uninitialized | Yes (dangerous) | No, must initialize |

A common guideline: prefer references when a value must always exist and won't need to be reassigned or point to nothing — such as function parameters. Use pointers when you need the flexibility of "no value" or reseating to a different object.

```cpp
void increment(int& value) { // reference: cleaner call syntax
    value++;
}

void increment(int* value) { // pointer: caller must check for null
    if (value != nullptr) {
        (*value)++;
    }
}

int x = 5;
increment(x);   // reference version, called normally
increment(&x);  // pointer version, requires taking the address
```

---

## 11.4 Pointer Arithmetic Basics

Pointers can be shifted to move through memory, most commonly with arrays:

```cpp
int numbers[] = {10, 20, 30};
int* p = numbers; // points to the first element

std::cout << *p;       // 10
std::cout << *(p + 1); // 20, one int-width past p
std::cout << *(p + 2); // 30
```

Pointer arithmetic accounts for the size of the pointed-to type automatically — `p + 1` moves forward by `sizeof(int)` bytes, not by one byte. This is covered further in **Arrays & C-Style Strings**.

---

## 11.5 const With Pointers And References

`const` can apply to the pointer itself, the value it points to, or both:

```cpp
int value = 10;

const int* ptrToConst = &value;  // can't modify *ptrToConst, but ptr can be reassigned
int* const constPtr = &value;    // ptr can't be reassigned, but *constPtr can be modified
const int* const constPtrToConst = &value; // neither can change

const int& constRef = value; // a reference that can't be used to modify value
```

`const` references are especially common as function parameters — they avoid copying large objects while promising the function won't modify the caller's data:

```cpp
void printName(const std::string& name) {
    std::cout << name << "\n";
    // name = "changed"; // compile error, name is const
}
```

[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[12]-Error-Handling.md)
