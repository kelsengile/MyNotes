[Previous](./[10]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[12]-Lists-Tuples-and-Sets.md)

# Lesson 11 - Error Handling

---

## 11.1 try, except, else, finally


```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
else:
    print("This runs only if no exception occurred")
finally:
    print("This always runs, error or not")
```

- `try` — code that might raise an exception.
- `except` — runs if a matching exception occurs.
- `else` — runs only if the `try` block succeeded (no exception).
- `finally` — always runs, useful for cleanup (closing files, releasing resources).

---

## 11.2 Catching Specific Exceptions


Always catch the most specific exception type you can — a bare `except:` silently swallows everything, including typos and `KeyboardInterrupt`, which makes bugs much harder to find.

```python
try:
    value = int(input("Enter a number: "))
except ValueError:
    print("That wasn't a valid number")
except KeyboardInterrupt:
    print("Cancelled by user")

# Multiple exception types in one clause:
try:
    risky_operation()
except (TypeError, ValueError) as e:
    print(f"Something went wrong: {e}")
```

---

## 11.3 Raising Exceptions


Use `raise` to signal that something has gone wrong in your own code:

```python
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError("Insufficient funds")
    return balance - amount
```

You can also re-raise an exception you've caught, optionally after logging or partial handling:

```python
try:
    risky_operation()
except ValueError:
    print("Logging the error...")
    raise   # re-raises the same exception
```

---

## 11.4 Custom Exceptions


For domain-specific errors, define your own exception class by inheriting from `Exception` (or a more specific built-in exception):

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

Custom exceptions make error handling more precise and self-documenting than relying only on generic built-in exception types. Class inheritance is covered fully in Lesson 17.

---

[Previous](./[10]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[12]-Lists-Tuples-and-Sets.md)
