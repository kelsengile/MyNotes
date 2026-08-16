[Previous](./[11]-References-vs-Pointers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[13]-Stack-vs-Heap.md)

*Core Syntax*

# Lesson 12 - Error Handling

## 12.1 Why Error Handling Matters

Programs constantly encounter situations they can't just push through: a file that doesn't exist, a division by zero, invalid user input. Without a plan for handling these, a program either crashes ungracefully or silently produces wrong results. C++'s primary mechanism for handling unexpected error conditions is **exceptions**.

---

## 12.2 Exceptions: throw, try, catch

When code detects an error it can't recover from locally, it can **throw** an exception, which immediately stops normal execution and searches up the call stack for a matching **catch** block:

```cpp
#include <stdexcept>

double divide(double a, double b) {
    if (b == 0) {
        throw std::runtime_error("Division by zero");
    }
    return a / b;
}

int main() {
    try {
        double result = divide(10, 0);
        std::cout << result << "\n"; // never reached
    } catch (const std::exception& e) {
        std::cout << "Error: " << e.what() << "\n";
    }
}
```

Code that might throw goes in a `try` block; the matching `catch` block runs if a matching exception is thrown. If nothing catches it, the program terminates.

---

## 12.3 Standard Exception Types

The `<stdexcept>` header provides a hierarchy of common exception types, all deriving from `std::exception`:

```cpp
std::runtime_error   // general runtime errors
std::logic_error     // errors detectable before runtime, like bad arguments
std::out_of_range    // e.g. accessing a container index that doesn't exist
std::invalid_argument // an argument doesn't meet requirements
```

Because they share a common base, a single `catch (const std::exception& e)` can catch any of them, while more specific catch blocks can handle particular types differently:

```cpp
try {
    // ...
} catch (const std::out_of_range& e) {
    std::cout << "Out of range: " << e.what() << "\n";
} catch (const std::exception& e) {
    std::cout << "Other error: " << e.what() << "\n";
}
```

---

## 12.4 noexcept

Marking a function `noexcept` promises it will never throw an exception:

```cpp
int square(int x) noexcept {
    return x * x;
}
```

This isn't just documentation — if a `noexcept` function *does* throw, the program calls `std::terminate` immediately rather than unwinding normally. It also lets the compiler skip some exception-handling overhead, which matters for performance-sensitive code like move constructors.

---

## 12.5 Best Practices For Exceptions

- Throw exceptions for genuinely **exceptional** situations, not routine control flow — checking a return value is often more appropriate for expected failures.
- Catch exceptions **by const reference** (`catch (const std::exception& e)`) to avoid unnecessary copies and slicing.
- Always clean up resources properly when an exception might be thrown — this is where **RAII**, covered later in this course, becomes essential.
- Prefer the standard exception types, or derive your own from `std::exception`, rather than throwing raw values like integers or strings.

[Previous](./[11]-References-vs-Pointers.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[13]-Stack-vs-Heap.md)
