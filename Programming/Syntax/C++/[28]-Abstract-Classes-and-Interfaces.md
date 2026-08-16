[Previous](./[27]-Operator-Overloading.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[29]-Multiple-and-Virtual-Inheritance.md)

*Object-Oriented Programming*

# Lesson 28 - Abstract Classes And Interfaces

## 28.1 Pure Virtual Functions

A **pure virtual function** is declared with `= 0` and has no implementation in its class — it exists purely to define a required signature that derived classes must implement:

```cpp
class Shape {
public:
    virtual double area() const = 0; // pure virtual: no body
};
```

Any class containing at least one pure virtual function becomes an **abstract class**, covered next.

---

## 28.2 Abstract Classes

An abstract class **cannot be instantiated directly** — you can't create a `Shape` object on its own, since `area()` has no implementation to call:

```cpp
// Shape s; // compile error: Shape is abstract

class Circle : public Shape {
public:
    Circle(double r) : radius(r) {}

    double area() const override { // must override, or Circle is also abstract
        return 3.14159 * radius * radius;
    }

private:
    double radius;
};

Circle c(5);
std::cout << c.area(); // works, Circle implements area()
```

Abstract classes exist to define a **contract**: "any concrete subclass must provide these behaviors," while leaving the actual implementation up to each derived class.

---

## 28.3 Interfaces In C++

Unlike some languages, C++ has no dedicated `interface` keyword — the convention is to use a class made up **entirely** of pure virtual functions, with no data members:

```cpp
class Drawable {
public:
    virtual void draw() const = 0;
    virtual ~Drawable() = default; // see 28.4
};

class Printable {
public:
    virtual void print() const = 0;
    virtual ~Printable() = default;
};

class Widget : public Drawable, public Printable {
public:
    void draw() const override { std::cout << "Drawing widget\n"; }
    void print() const override { std::cout << "Printing widget\n"; }
};
```

A class can implement multiple such "interfaces" through multiple inheritance, covered in more depth in the next lesson.

---

## 28.4 Virtual Destructors

If a class has any virtual functions and will be deleted through a base class pointer, its destructor **must** also be `virtual` — otherwise, deleting through the base pointer only runs the base class's destructor, leaking any resources owned by the derived part of the object:

```cpp
class Base {
public:
    virtual ~Base() = default; // essential when using polymorphism with new/delete
};

class Derived : public Base {
public:
    ~Derived() override {
        std::cout << "Derived cleanup\n";
    }
private:
    int* data = new int[100];
};

Base* b = new Derived();
delete b; // with a virtual destructor, this correctly calls ~Derived() first
```

As a rule: any class intended to be used polymorphically (i.e., through base class pointers) should declare its destructor `virtual`, even if it does nothing itself.

[Previous](./[27]-Operator-Overloading.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[29]-Multiple-and-Virtual-Inheritance.md)
