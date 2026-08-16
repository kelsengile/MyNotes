[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[13]-Arrays.md)

*Core Syntax*

# Lesson 12 - Error And Exception Handling

## 12.1 Why Handle Errors?

Things go wrong: a file might not exist, a database connection could fail, user input might be invalid. Instead of letting a script crash, PHP lets you catch these problems and respond gracefully.

---

## 12.2 try, catch, finally

Code that might fail goes in a `try` block; `catch` handles the failure; `finally` always runs, whether or not an exception occurred:

```php
<?php
try {
    $result = 10 / 0;
} catch (\DivisionByZeroError $e) {
    echo "Error: " . $e->getMessage();
} finally {
    echo "Done attempting the operation.";
}
```

---

## 12.3 Throwing and Creating Custom Exceptions

Use `throw` to raise your own exceptions, and extend PHP's built-in `Exception` class to create domain-specific ones:

```php
<?php
class InsufficientFundsException extends \Exception {}

function withdraw(float $balance, float $amount): float {
    if ($amount > $balance) {
        throw new InsufficientFundsException("Not enough funds.");
    }
    return $balance - $amount;
}

try {
    withdraw(50, 100);
} catch (InsufficientFundsException $e) {
    echo $e->getMessage(); // Not enough funds.
}
```

---

## 12.4 Multiple catch Blocks and Exception Hierarchies

You can catch different exception types separately, ordered from most specific to most general. A single `catch` can also list multiple types with `|`:

```php
<?php
try {
    // risky code
} catch (InsufficientFundsException $e) {
    echo "Funds issue: " . $e->getMessage();
} catch (\TypeError | \ValueError $e) {
    echo "Bad input: " . $e->getMessage();
} catch (\Throwable $e) {
    echo "Unexpected error: " . $e->getMessage();
}
```

`\Throwable` is the top-level interface implemented by both `Exception` and `Error`, so catching it handles anything that could go wrong.

---

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[13]-Arrays.md)
