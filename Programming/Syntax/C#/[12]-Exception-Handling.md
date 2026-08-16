[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[13]-Arrays-and-Lists.md)

*Core Syntax*

# Lesson 12 - Exception Handling: try, catch, finally, custom exceptions

## 12.1 try / catch / finally

An **exception** is an error that occurs while a program runs. `try`/`catch` lets you handle it gracefully instead of crashing:

```csharp
try
{
    int[] numbers = { 1, 2, 3 };
    Console.WriteLine(numbers[5]);   // out of range
}
catch (IndexOutOfRangeException ex)
{
    Console.WriteLine($"Error: {ex.Message}");
}
finally
{
    Console.WriteLine("This always runs, error or not.");
}
```

The `finally` block runs whether or not an exception was thrown — commonly used to release resources like open files or connections.

---

## 12.2 Common Exception Types

| Exception | Thrown when |
|---|---|
| `NullReferenceException` | Accessing a member on a `null` object |
| `IndexOutOfRangeException` | Accessing an array index that doesn't exist |
| `DivideByZeroException` | Dividing an integer by zero |
| `FormatException` | Parsing text into the wrong type (e.g. `int.Parse("abc")`) |
| `ArgumentException` | An argument passed to a method is invalid |

You can catch multiple exception types with separate `catch` blocks, ordered from most to least specific:

```csharp
try { /* ... */ }
catch (FormatException ex) { /* handle bad input */ }
catch (Exception ex) { /* handle anything else */ }
```

---

## 12.3 Custom Exceptions

You can define your own exception types by inheriting from `Exception` — useful for representing domain-specific error conditions clearly:

```csharp
public class InsufficientFundsException : Exception
{
    public InsufficientFundsException(string message) : base(message) { }
}

void Withdraw(decimal amount, decimal balance)
{
    if (amount > balance)
        throw new InsufficientFundsException("Not enough funds to complete withdrawal.");
}
```

---

## 12.4 Best Practices

- Only catch exceptions you can meaningfully handle — don't swallow errors silently with an empty `catch` block.
- Catch specific exception types before general ones.
- Avoid using exceptions for normal control flow (e.g. prefer `int.TryParse` over catching a `FormatException`).
- Always clean up resources — a `using` statement (see [Lesson 36](./[36]-Working-with-Files.md)) often replaces the need for a manual `finally` block.

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[13]-Arrays-and-Lists.md)
