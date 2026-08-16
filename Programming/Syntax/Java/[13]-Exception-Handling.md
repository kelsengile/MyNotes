[Previous](./[12]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[14]-OOP-Classes-and-Objects.md)

*Core Syntax*

# Lesson 13 - Exception Handling: try, catch, finally, throw, custom exceptions

Things go wrong at runtime — files go missing, input is invalid, networks fail. This lesson covers how Java lets you detect and respond to these problems gracefully.

## 13.1 What Are Exceptions?

An **exception** is an object representing an error or unusual condition that disrupts normal program flow. When something goes wrong, Java **throws** an exception, which — if not handled — propagates up and eventually crashes the program with a stack trace.

```java
int[] numbers = {1, 2, 3};
System.out.println(numbers[5]); // throws ArrayIndexOutOfBoundsException
```

---

## 13.2 try, catch, finally

You handle exceptions with a `try` block (code that might fail), one or more `catch` blocks (what to do if it does), and an optional `finally` block (code that always runs, whether an exception occurred or not):

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Can't divide by zero: " + e.getMessage());
} finally {
    System.out.println("This always runs.");
}
```

`finally` is commonly used for cleanup work, like closing a file or network connection, that must happen regardless of success or failure.

---

## 13.3 Checked vs Unchecked Exceptions

Java distinguishes two categories of exceptions:

- **Checked exceptions** (subclasses of `Exception`, excluding `RuntimeException`) — the compiler *forces* you to either catch them or declare that your method might throw them. Typically represent recoverable conditions outside your program's control, like a missing file (`IOException`).
- **Unchecked exceptions** (subclasses of `RuntimeException`) — the compiler does not require handling. Typically represent programming errors, like `NullPointerException` or `ArrayIndexOutOfBoundsException`.

This distinction shapes how you design error handling — checked exceptions signal "the caller must decide what to do," while unchecked exceptions usually signal a bug to fix.

---

## 13.4 throw and throws

- **`throw`** is used inside a method to actually raise an exception.
- **`throws`** appears in a method's signature to declare that it *might* throw a checked exception, passing the responsibility to whoever calls it.

```java
public static void withdraw(double amount, double balance) throws InsufficientFundsException {
    if (amount > balance) {
        throw new InsufficientFundsException("Not enough funds.");
    }
}
```

---

## 13.5 Custom Exceptions

You can define your own exception types by extending `Exception` (checked) or `RuntimeException` (unchecked), which is useful for representing domain-specific error conditions clearly:

```java
public class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}
```

Custom exceptions make error handling more expressive than relying only on generic built-in types, since callers can catch exactly the failure they care about.

---

## 13.6 try-with-resources

When working with resources that need closing (files, database connections, network sockets), `try-with-resources` automatically closes them for you at the end of the block, even if an exception occurs:

```java
try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
    System.out.println(reader.readLine());
} catch (IOException e) {
    System.out.println("Failed to read file: " + e.getMessage());
}
```

Any resource that implements the `AutoCloseable` interface can be used this way — no manual `finally` block needed to close it. We'll use this pattern extensively once we reach file I/O in [Lesson 43](./[43]-Working-with-Files.md).

---

[Previous](./[12]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[14]-OOP-Classes-and-Objects.md)