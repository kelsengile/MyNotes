[Previous](./[23]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[25]-Inheritance-and-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 24 - Rule Of Three, Five, And Zero

## 24.1 Constructors

A **constructor** is a special member function that runs automatically when an object is created, typically used to initialize its members:

```cpp
class Point {
public:
    Point(double x, double y) : x(x), y(y) { // member initializer list
        std::cout << "Point created\n";
    }

    double x, y;
};

Point p(3.0, 4.0); // calls the constructor
```

The `: x(x), y(y)` syntax is a **member initializer list** — it initializes members directly, and is preferred over assigning them inside the constructor body.

---

## 24.2 Destructors

A **destructor**, named `~ClassName()`, runs automatically when an object is destroyed — going out of scope, or being `delete`d. It's where RAII-style cleanup happens:

```cpp
class Buffer {
public:
    Buffer(size_t n) : data(new int[n]) {}
    ~Buffer() {
        delete[] data; // cleanup happens automatically
        std::cout << "Buffer destroyed\n";
    }
private:
    int* data;
};
```

---

## 24.3 Copy Constructor And Copy Assignment

When a class manages a resource like heap memory, the compiler-generated default copy behavior — copying each member field for field — is dangerous, because it copies the pointer, not the data it points to:

```cpp
class Buffer {
public:
    Buffer(size_t n) : data(new int[n]), size(n) {}

    // Copy constructor: define what "copy" actually means
    Buffer(const Buffer& other) : data(new int[other.size]), size(other.size) {
        std::copy(other.data, other.data + size, data);
    }

    // Copy assignment operator
    Buffer& operator=(const Buffer& other) {
        if (this == &other) return *this; // guard against self-assignment
        delete[] data;
        size = other.size;
        data = new int[size];
        std::copy(other.data, other.data + size, data);
        return *this;
    }

    ~Buffer() { delete[] data; }

private:
    int* data;
    size_t size;
};
```

Without a proper copy constructor, two `Buffer` objects would end up pointing to the same heap memory, and both destructors would try to free it — a double free.

---

## 24.4 The Rule Of Three

The **Rule of Three** states: if a class needs to define **any one** of a destructor, copy constructor, or copy assignment operator, it almost certainly needs to define **all three**. This is because needing one is a sign the class manages a resource (like raw memory) that the compiler's default member-by-member behavior can't handle safely.

---

## 24.5 The Rule Of Five And Rule Of Zero

C++11 added move semantics, extending this to the **Rule of Five**: also define a **move constructor** and **move assignment operator** for the same reason, so the class can transfer ownership efficiently instead of always copying:

```cpp
Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
    other.data = nullptr;
    other.size = 0;
}

Buffer& operator=(Buffer&& other) noexcept {
    if (this == &other) return *this;
    delete[] data;
    data = other.data;
    size = other.size;
    other.data = nullptr;
    other.size = 0;
    return *this;
}
```

The **Rule of Zero** offers a better alternative wherever possible: design classes so they **don't** manage raw resources directly at all — instead, use members like `std::vector` or `std::unique_ptr` that already implement RAII correctly. Then the compiler-generated destructor, copy, and move operations all just work, and you don't need to write any of the five functions yourself.

[Previous](./[23]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[25]-Inheritance-and-Polymorphism.md)
