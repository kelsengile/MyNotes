[Previous](./[16]-Smart-Pointers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[18]-Arrays-and-C-Strings.md)

*Memory Management*

# Lesson 17 - Move Semantics

## 17.1 Lvalues And Rvalues

Every C++ expression is either an **lvalue** or an **rvalue**. Roughly: an lvalue has a persistent identity and a name you can take the address of; an rvalue is a temporary that's about to disappear.

```cpp
int x = 10;      // x is an lvalue
int y = x + 5;    // x + 5 is an rvalue: a temporary result with no name

std::string s1 = "hello";       // s1: lvalue
std::string s2 = s1 + " world"; // s1 + " world": rvalue (a temporary string)
```

This distinction matters because rvalues, being temporaries that are about to be destroyed anyway, are safe to "steal" resources from instead of copying.

---

## 17.2 Rvalue References

An **rvalue reference**, written `T&&`, binds specifically to rvalues (temporaries):

```cpp
void process(std::string&& s) {
    std::cout << "Got a temporary: " << s << "\n";
}

process(std::string("hello")); // binds fine: this is a temporary

std::string name = "Alice";
// process(name); // compile error: name is an lvalue, not an rvalue
```

This lets you write a function overload specifically for the case where the caller's value is about to be discarded anyway — which is exactly what move semantics take advantage of.

---

## 17.3 Move Constructors And Move Assignment

A class can define a **move constructor** and **move assignment operator** that transfer ownership of internal resources (like a heap buffer) instead of copying them:

```cpp
class Buffer {
public:
    Buffer(size_t size) : data(new int[size]), size(size) {}

    // Move constructor: steal the pointer, leave the source empty
    Buffer(Buffer&& other) noexcept
        : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
    }

    ~Buffer() { delete[] data; }

private:
    int* data;
    size_t size;
};
```

Instead of allocating a new buffer and copying every element, the move constructor just copies the pointer and nulls out the source — an O(1) operation regardless of the buffer's size.

---

## 17.4 std::move

`std::move` doesn't actually move anything itself — it's just a cast that tells the compiler "treat this lvalue as an rvalue," making it eligible for move operations:

```cpp
#include <utility>

std::string a = "hello";
std::string b = std::move(a); // a's internal buffer is transferred to b

// a is now in a valid but unspecified state — don't rely on its contents,
// though it's still safe to assign a new value to it or destroy it
```

Only use `std::move` on a value you no longer need in its current form — after moving from it, its contents shouldn't be assumed.

---

## 17.5 When Moves Happen Automatically

The compiler automatically prefers a move over a copy whenever the source is already an rvalue — you don't need `std::move` in these cases:

```cpp
std::vector<std::string> makeNames() {
    std::vector<std::string> names = {"Alice", "Bob"};
    return names; // moved (or elided entirely) automatically, no copy
}

std::vector<std::string> result = makeNames(); // efficient, no manual std::move needed
```

This is a major reason modern C++ can return large objects like vectors and strings from functions efficiently, without the performance concerns that discouraged this pattern in older C++ code.

[Previous](./[16]-Smart-Pointers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[18]-Arrays-and-C-Strings.md)
