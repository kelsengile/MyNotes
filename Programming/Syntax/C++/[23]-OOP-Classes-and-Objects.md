[Previous](./[22]-STL-Iterators-and-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[24]-Rule-of-Three-Five-Zero.md)

*Object-Oriented Programming*

# Lesson 23 - Classes And Objects

## 23.1 Defining A Class

A **class** bundles data and the functions that operate on it into a single type. It's a blueprint; an **object** is a specific instance created from that blueprint:

```cpp
class Dog {
public:
    std::string name;
    int age;

    void bark() {
        std::cout << name << " says woof!\n";
    }
};
```

By default, class members are **private** unless marked otherwise — the `public:` label above makes everything after it accessible from outside the class. Access control is covered in depth in **Encapsulation & Access Control**.

---

## 23.2 Member Variables And Member Functions

**Member variables** (also called fields or attributes) store an object's data. **Member functions** (methods) define its behavior, and can access that object's own member variables directly, without needing them passed in as parameters:

```cpp
class Rectangle {
public:
    double width;
    double height;

    double area() {
        return width * height; // uses this object's own width and height
    }

    void scale(double factor) {
        width *= factor;
        height *= factor;
    }
};
```

---

## 23.3 Creating Objects

Objects can be created on the stack or the heap, just like any other variable:

```cpp
Rectangle r1;           // stack, members are uninitialized
r1.width = 4;
r1.height = 3;
std::cout << r1.area(); // 12

Rectangle r2{5, 2};      // stack, initialized via brace initialization
std::cout << r2.area(); // 10

Rectangle* r3 = new Rectangle{6, 4}; // heap
std::cout << r3->area(); // 24, note -> for accessing members through a pointer
delete r3;
```

Constructors, covered in the next lesson, provide a more robust way to guarantee an object starts in a valid state.

---

## 23.4 The this Pointer

Inside a member function, `this` is an implicit pointer to the object the function was called on. It's mostly used to disambiguate a member variable from a parameter with the same name, or to return the object itself for chaining:

```cpp
class Counter {
public:
    int count;

    Counter& increment() {
        this->count++; // this-> is optional here, count would work too
        return *this;   // returns a reference to this object
    }
};

Counter c;
c.count = 0;
c.increment().increment().increment(); // chained calls, count is now 3
```

[Previous](./[22]-STL-Iterators-and-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[24]-Rule-of-Three-Five-Zero.md)
