[Previous](./[16]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[18]-Abstraction-Interfaces-and-Abstract-Classes.md)

*Object-Oriented Programming*

# Lesson 17 - Encapsulation & Access Modifiers (public, private, protected, package-private)

Encapsulation is about controlling what parts of your code can access and modify an object's internal data. This lesson covers Java's access modifiers and how they support that goal.

## 17.1 What Is Encapsulation?

**Encapsulation** means bundling an object's data with the methods that operate on it, while restricting direct outside access to that data. Instead of letting any code freely change a field, you expose controlled entry points (methods), which lets you validate changes and protect the object from being put into an invalid state.

---

## 17.2 public, private, protected, package-private

Java has four access levels, applied to fields, methods, and constructors:

| Modifier | Same class | Same package | Subclass (other package) | Everywhere |
|---|---|---|---|---|
| `public` | ✅ | ✅ | ✅ | ✅ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| *(none — package-private)* | ✅ | ✅ | ❌ | ❌ |
| `private` | ✅ | ❌ | ❌ | ❌ |

```java
public class BankAccount {
    private double balance; // only accessible within this class
}
```

Leaving off a modifier entirely (no keyword) gives **package-private** access — visible only to other classes in the same package.

---

## 17.3 Getters and Setters

The standard way to expose controlled access to a `private` field is through **getter** and **setter** methods:

```java
public class BankAccount {
    private double balance;

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        if (balance < 0) {
            throw new IllegalArgumentException("Balance cannot be negative.");
        }
        this.balance = balance;
    }
}
```

The setter can validate input before allowing a change — something impossible if the field were simply `public` and modified directly.

---

## 17.4 Access Modifiers on Classes

Access modifiers also apply to entire top-level classes, though the options are more limited: a top-level class can only be `public` (visible everywhere) or package-private (visible only within its own package). `private` and `protected` are reserved for members (fields, methods, constructors) and nested classes — not top-level classes.

---

## 17.5 Best Practices

A widely followed convention in Java is: **make fields `private` by default**, and only expose what's truly necessary through `public` methods. This keeps an object's internal representation free to change later without breaking code elsewhere that depends on it — you can freely rewrite how `BankAccount` stores its balance internally, as long as `getBalance()` keeps working the same way from the outside.

---

[Previous](./[16]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[18]-Abstraction-Interfaces-and-Abstract-Classes.md)