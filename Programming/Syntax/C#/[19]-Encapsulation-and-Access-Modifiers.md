[Previous](./[18]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[20]-Interfaces-and-Abstract-Classes.md)

*Object-Oriented Programming*

# Lesson 19 - Encapsulation & Access Modifiers

## 19.1 What is Encapsulation?

**Encapsulation** means hiding an object's internal details and only exposing what's necessary through a controlled interface. Instead of letting outside code freely change a field to any value, you control access through properties and methods — protecting the object from being put into an invalid state.

```csharp
public class BankAccount
{
    private decimal _balance;   // hidden from outside code

    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Deposit must be positive.");
        _balance += amount;
    }

    public decimal GetBalance() => _balance;
}
```

Outside code can't set `_balance` directly to a negative number — it can only go through `Deposit`, which enforces the rule.

---

## 19.2 Access Modifiers

Access modifiers control which parts of your code can see a member:

| Modifier | Accessible from |
|---|---|
| `public` | Anywhere |
| `private` | Only within the same class (the default if unspecified) |
| `protected` | The same class and any class that inherits from it |
| `internal` | Anywhere in the same project/assembly |
| `protected internal` | Same assembly, or any derived class |

```csharp
public class Animal
{
    private string _id;        // only Animal itself
    protected string Name;     // Animal and its subclasses
    public int Age;            // anyone
    internal string Species;   // anywhere in this project
}
```

---

## 19.3 Encapsulation in Practice

A common pattern combines a `private` backing field with a `public` property, so the class can control what values are allowed while still presenting a simple interface to callers:

```csharp
public class Person
{
    private int _age;

    public int Age
    {
        get => _age;
        set
        {
            if (value < 0)
                throw new ArgumentException("Age cannot be negative.");
            _age = value;
        }
    }
}
```

[Previous](./[18]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[20]-Interfaces-and-Abstract-Classes.md)
