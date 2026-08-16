[Previous](./[25]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[27]-Operator-Overloading.md)

*Object-Oriented Programming*

# Lesson 26 - Encapsulation And Access Control

## 26.1 public, private, protected

C++ classes control access to their members with three keywords:

```cpp
class BankAccount {
public:
    // accessible from anywhere
    void deposit(double amount) {
        balance += amount;
    }

private:
    // only accessible from within this class's own member functions
    double balance = 0.0;

protected:
    // accessible from this class AND classes that inherit from it
    std::string accountId;
};
```

By default, members of a `class` are `private`; members of a `struct` are `public`. `private` is the right default for internal implementation details you don't want outside code depending on directly.

---

## 26.2 Getters And Setters

Rather than exposing member variables directly, it's common to provide controlled access through member functions, so the class can validate changes or change its internal representation later without breaking external code:

```cpp
class BankAccount {
public:
    double getBalance() const { // const: promises not to modify the object
        return balance;
    }

    void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        }
    }

private:
    double balance = 0.0;
};
```

Here, `balance` can never be set to an invalid value directly from outside the class — all changes go through methods that enforce the account's rules.

---

## 26.3 Friend Functions And Classes

A `friend` declaration grants a specific external function or class access to a class's private members — an explicit, opt-in exception to normal access control:

```cpp
class Box {
public:
    Box(double w) : width(w) {}

    friend void printWidth(const Box& box); // grants access to this one function

private:
    double width;
};

void printWidth(const Box& box) {
    std::cout << box.width; // allowed, because printWidth is a friend
}
```

Friendship is not inherited and not transitive — it should be used sparingly, typically for closely related helper functions like overloaded operators (covered in the next lesson).

---

## 26.4 Why Encapsulation Matters

**Encapsulation** — bundling data with the functions that operate on it, and restricting direct access to that data — provides several concrete benefits:

- **Invariant enforcement** — a class can guarantee its data always stays in a valid state (e.g. `balance` never goes negative).
- **Implementation freedom** — internal representation can change without breaking code that uses the class, as long as the public interface stays the same.
- **Reduced coupling** — other code depends only on a class's public interface, not its internal details, making large codebases easier to change safely over time.

A well-encapsulated class exposes a minimal, clear public interface and hides everything else — a principle worth applying by default, even in small programs.

[Previous](./[25]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[27]-Operator-Overloading.md)
