[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[13]-Lists-Tuples-and-Sets.md)

*Core Syntax*

# Lesson 12 - Error Handling

## 12.1 What Are Exceptions?

An **exception** is an event that occurs when Python encounters an error while running your code, interrupting the normal flow of execution. If unhandled, it crashes the program and prints a **traceback**:

```python
result = 10 / 0
# ZeroDivisionError: division by zero
```

Common built-in exceptions include `ValueError`, `TypeError`, `KeyError`, `IndexError`, `ZeroDivisionError`, and `FileNotFoundError`.

---

## 12.2 try / except

Wrap risky code in a `try` block, and handle the error in an `except` block instead of letting the program crash:

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero!")
```

---

## 12.3 else and finally

- **`else`** runs only if the `try` block completed with **no** exception.
- **`finally`** always runs, whether an exception occurred or not — commonly used for cleanup (like closing a file).

```python
try:
    number = int(input("Enter a number: "))
except ValueError:
    print("That wasn't a valid number.")
else:
    print(f"You entered {number}")
finally:
    print("Done processing input.")
```

---

## 12.4 Catching Multiple/Specific Exceptions

You can catch several exception types, either separately or together, and access the exception object itself:

```python
try:
    value = int("abc")
except (ValueError, TypeError) as e:
    print(f"Conversion failed: {e}")

try:
    risky_operation()
except ValueError:
    print("Bad value")
except KeyError:
    print("Missing key")
except Exception as e:
    print(f"Something else went wrong: {e}")   # generic catch-all, last
```

Always catch the **most specific** exception you can — catching a broad `Exception` hides bugs by silently swallowing errors you didn't anticipate.

---

## 12.5 Raising Exceptions

Use `raise` to trigger an exception yourself, typically to enforce a precondition:

```python
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError("Insufficient funds")
    return balance - amount
```

You can also re-raise the current exception inside an `except` block (useful for logging before propagating it further) with a bare `raise`.

---

## 12.6 Custom Exceptions

For domain-specific errors, define your own exception classes by inheriting from `Exception` (or a more specific built-in exception):

```python
class InsufficientFundsError(Exception):
    """Raised when a withdrawal exceeds the available balance."""
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(f"Cannot withdraw {amount}, balance is {balance}")
    return balance - amount

try:
    withdraw(100, 150)
except InsufficientFundsError as e:
    print(e)
```

Custom exceptions make error handling more expressive and let callers catch precisely the errors relevant to your application, rather than generic built-in ones.

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[13]-Lists-Tuples-and-Sets.md)
