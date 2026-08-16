[Previous](./[26]-Encapsulation-and-Access-Control.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[28]-Abstract-Classes-and-Interfaces.md)

*Object-Oriented Programming*

# Lesson 27 - Operator Overloading

## 27.1 Why Overload Operators

C++ lets you define what operators like `+`, `==`, or `<<` mean for your own classes, so objects can be used with the same natural syntax as built-in types:

```cpp
class Vector2D {
public:
    double x, y;
};

Vector2D a{1, 2}, b{3, 4};
// Vector2D c = a + b; // won't compile yet — + isn't defined for Vector2D
```

Overloading `+` for `Vector2D` lets code read naturally as `a + b`, instead of something like `add(a, b)`.

---

## 27.2 Overloading Arithmetic Operators

```cpp
class Vector2D {
public:
    double x, y;

    Vector2D operator+(const Vector2D& other) const {
        return Vector2D{x + other.x, y + other.y};
    }

    Vector2D operator*(double scalar) const {
        return Vector2D{x * scalar, y * scalar};
    }
};

Vector2D a{1, 2}, b{3, 4};
Vector2D sum = a + b;      // {4, 6}
Vector2D scaled = a * 2.0; // {2, 4}
```

The `const` at the end promises the operator won't modify `*this` — appropriate here, since `+` shouldn't change either operand.

---

## 27.3 Overloading Comparison Operators

```cpp
class Vector2D {
public:
    double x, y;

    bool operator==(const Vector2D& other) const {
        return x == other.x && y == other.y;
    }

    bool operator!=(const Vector2D& other) const {
        return !(*this == other); // reuse == instead of duplicating logic
    }
};

Vector2D a{1, 2}, b{1, 2};
bool same = (a == b); // true
```

Since C++20, defining `operator<=>` (the "spaceship operator") can automatically generate `<`, `<=`, `>`, and `>=` from a single function, reducing boilerplate for ordered comparisons.

---

## 27.4 Overloading << And >>

Overloading the stream insertion operator `<<` lets objects be printed directly with `std::cout`. Because the left-hand operand is `std::ostream`, not your class, this overload must be a **free function**, not a member:

```cpp
class Vector2D {
public:
    double x, y;
};

std::ostream& operator<<(std::ostream& os, const Vector2D& v) {
    os << "(" << v.x << ", " << v.y << ")";
    return os; // return the stream so calls can be chained
}

Vector2D a{1, 2};
std::cout << a << "\n"; // prints "(1, 2)"
```

`operator>>` for `std::cin` follows the same pattern, taking `std::istream&` instead.

---

## 27.5 Rules And Best Practices

- Only overload an operator when its meaning for your type is genuinely intuitive — overloading `+` to mean something unrelated to addition confuses readers.
- Keep overloaded operators consistent with each other (e.g. if `==` is defined, `!=` should have the logically opposite meaning).
- Prefer member functions for operators that naturally belong to the class (like `+`, `-`, `*`), and free functions for operators where the left operand isn't your class (like `<<` with `std::ostream`).
- Mark operators `const` whenever they don't modify the object, so they can be used on `const` instances too.

[Previous](./[26]-Encapsulation-and-Access-Control.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[28]-Abstract-Classes-and-Interfaces.md)
