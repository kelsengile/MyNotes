[Previous](./[28]-Equality-and-Immutability.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[30]-Assertions.md)

*Error Handling*

# Lesson 29 - Exceptions & Error Handling

Things go wrong: a file is missing, the user types text where a number is expected, the network drops, or the code itself has a bug. An **exception** is Dart's way of signaling that something unexpected happened. When one is **thrown**, normal execution stops and Dart searches upward through the calling functions for code that **catches** it. If nothing does, the program (or the current isolate) ends with an error message and a stack trace.

---

## 29.1 `throw`

You raise an exception with the `throw` keyword:

```dart
void checkAge(int age) {
  if (age < 0) {
    throw ArgumentError('Age cannot be negative: $age');
  }
  print('Age is $age');
}

void main() {
  checkAge(25);    // Age is 25
  checkAge(-3);    // throws, and the program stops here if nobody catches it
  print('never reached');
}
```

An uncaught exception prints something like `Unhandled exception: Invalid argument(s): Age cannot be negative: -3` followed by a stack trace (the list of calls that led to the error).

### `throw` is an expression

Because `throw` is an expression, you can use it anywhere a value is expected, such as in `=>` functions or after `??`:

```dart
String requireName(String? name) => name ?? throw ArgumentError('name is required');

int parseOrFail(String text) =>
    int.tryParse(text) ?? (throw FormatException('Not a number', text));
```

### What can be thrown?

Dart allows you to throw **any non-null object**, even a string or a number:

```dart
throw 'Something broke';   // legal, but poor style
throw 42;                  // legal, but poor style
```

Do not do this. A string has no type to catch selectively, no stack trace, and no structure. Always throw an instance of `Exception`, `Error`, or one of their subclasses (see 29.4 and 29.5). The `only_throw_errors` lint enforces this.

### Common built-in exceptions and errors

| Type | Typical cause |
|---|---|
| `FormatException` | Text is not in the expected format, such as `int.parse('abc')` |
| `ArgumentError` | A function received an invalid argument |
| `RangeError` | An index or value is outside the allowed range, such as `[1, 2][5]` |
| `StateError` | An operation is not allowed in the object's current state, such as `[].first` |
| `UnsupportedError` | The operation is not supported, such as adding to an unmodifiable list |
| `UnimplementedError` | A feature that is not written yet |
| `TypeError` | A value was used as the wrong type |
| `TimeoutException` | An operation took too long (`dart:async`, Lesson 31) |
| `IOException` and subclasses | File and network problems (`dart:io`, Lesson 41) |

```dart
void main() {
  print(int.tryParse('abc'));   // null: the "safe" alternative that does not throw
  print(int.parse('abc'));      // throws FormatException
}
```

Many libraries offer both styles: a method that **throws** on failure (`int.parse`) and one that returns `null` or a default (`int.tryParse`). Choose the second when failure is normal and expected.

---

## 29.2 `try`, `catch`, `on`, and `finally`

To handle an exception, put risky code inside a `try` block and describe the recovery in one or more `catch` or `on` clauses.

```dart
void main() {
  try {
    var n = int.parse('abc');
    print('Parsed $n');          // skipped: the line above threw
  } on FormatException catch (e) {
    print('Not a number: ${e.source}');   // Not a number: abc
  }

  print('Program continues');
}
```

### The three clause forms

| Form | Meaning |
|---|---|
| `on Type { ... }` | Catch only exceptions of that type; you do not need the object |
| `on Type catch (e) { ... }` | Catch that type **and** give the object a name |
| `catch (e) { ... }` | Catch **anything** thrown (the object is typed `Object`) |

`catch` accepts a second parameter for the **stack trace**:

```dart
try {
  riskyOperation();
} catch (e, stackTrace) {
  print('Error: $e');
  print('Where it happened:\n$stackTrace');
}
```

### Catching selectively

Use `on` to handle each kind of failure differently. Dart tries the clauses **top to bottom** and runs the **first one that matches**, so put the most specific types first and the general ones last:

```dart
void load(String text) {
  try {
    var index = int.parse(text);
    var fruits = ['apple', 'banana'];
    print('Fruit: ${fruits[index]}');
  } on FormatException {
    print('Please enter digits only.');
  } on RangeError {
    print('No fruit at that position.');
  } on Exception catch (e) {
    print('Some other problem: $e');          // any remaining Exception
  } catch (e) {
    print('Unexpected: $e');                  // anything else, including other Errors
  }
}

void main() {
  load('1');      // Fruit: banana
  load('abc');    // Please enter digits only.
  load('5');      // No fruit at that position.
}
```

Catching too broadly (a bare `catch (e)` everywhere) hides real bugs. Catch the specific problems you know how to recover from, and let the rest propagate.

### `finally`

A `finally` block runs **no matter what**: after the `try` finishes normally, after a `catch` handles an error, and even if an exception is uncaught or the block returns early. Use it for cleanup such as closing files, connections, or stopping a timer:

```dart
void work() {
  print('Opening resource');
  try {
    print('Working...');
    throw StateError('Something went wrong');
  } on StateError catch (e) {
    print('Handled: ${e.message}');
  } finally {
    print('Closing resource');      // always runs
  }
}

void main() {
  work();
  // Opening resource
  // Working...
  // Handled: Something went wrong
  // Closing resource
}
```

`finally` also runs when there is no `catch` at all (`try { ... } finally { ... }`), in which case the exception continues upward after the cleanup.

```dart
int compute() {
  try {
    return 1;
  } finally {
    print('cleanup');     // prints before the function actually returns 1
  }
}
```

Avoid putting `return`, `break`, or `throw` inside `finally`; it can silently replace the original result or exception.

### Exceptions travel up the call stack

You do not have to catch an exception where it is thrown. It moves up through each caller until something handles it, so catch it at the level that knows what to do:

```dart
int readAge(String text) => int.parse(text);       // throws, but does not handle

void main() {
  try {
    print(readAge('twenty'));
  } on FormatException {
    print('Invalid age');       // the caller decides how to respond
  }
}
```

(Errors in asynchronous code are handled with the same ideas plus a few additions; see Lesson 31.)

---

## 29.3 `rethrow`

Sometimes you want to **react** to an error, for example by logging it or cleaning up, but you cannot truly recover from it and the caller should still find out. Use `rethrow` inside a `catch` block to pass the **same exception** onward:

```dart
void process(String data) {
  try {
    var value = int.parse(data);
    print('Value: $value');
  } on FormatException catch (e) {
    print('Logging failure: ${e.source}');
    rethrow;                      // the caller receives the same exception
  }
}

void main() {
  try {
    process('oops');
  } on FormatException {
    print('Main handled it');
  }
  // Logging failure: oops
  // Main handled it
}
```

### `rethrow` vs `throw e`

```dart
} catch (e) {
  throw e;       // starts a NEW throw: the stack trace now begins here
  rethrow;       // keeps the ORIGINAL stack trace pointing at where it first failed
}
```

`rethrow` preserves the original stack trace, which is usually what you need for debugging. It can only be used inside a `catch` clause.

### Throwing a different exception but keeping the stack trace

Often you want to translate a low-level error into one that makes sense to your caller (a "database error" into a "could not load user"). To keep the original trace, use `Error.throwWithStackTrace`:

```dart
class UserLoadException implements Exception {
  final String message;
  final Object cause;
  const UserLoadException(this.message, this.cause);

  @override
  String toString() => 'UserLoadException: $message (cause: $cause)';
}

void loadUser(String id) {
  try {
    int.parse(id);               // pretend this is a risky database call
  } catch (e, stackTrace) {
    Error.throwWithStackTrace(
      UserLoadException('Could not load user $id', e),
      stackTrace,                // the trace of the original failure
    );
  }
}
```

### Typical reasons to rethrow

- Logging, then letting the caller decide.
- Cleaning up a partial change, then reporting the failure.
- Catching a broad type, handling just the cases you understand, and rethrowing the rest:

```dart
try {
  doWork();
} on Exception catch (e) {
  if (e is FormatException) {
    print('Fixing formatting problem');
  } else {
    rethrow;       // not ours to handle
  }
}
```

---

## 29.4 `Exception` vs `Error`

Dart separates problems into two families. Knowing which is which tells you whether to catch it.

| | `Exception` | `Error` |
|---|---|---|
| What it means | An expected, **recoverable** condition caused by the outside world | A **bug** in the program that should be fixed |
| Examples | `FormatException`, `TimeoutException`, `IOException` | `ArgumentError`, `RangeError`, `StateError`, `TypeError`, `UnsupportedError`, `UnimplementedError`, `AssertionError`, `StackOverflowError` |
| Should you catch it? | **Yes**, where you can recover | **Usually no**: fix the code instead |
| Has a stack trace property | No | Yes (`error.stackTrace`) |
| Who throws it | Library code and your code for external problems | Library code and your code when a caller misuses an API |

```dart
void main() {
  try {
    var list = [1, 2, 3];
    print(list[10]);
  } on RangeError catch (e) {
    print(e is Error);        // true: it is a bug, not an expected condition
  }
}
```

### How to decide

Ask: **"Could a correct program still hit this?"**

- A correct program can still receive a malformed number from a user, lose its network connection, or find a file missing. These are **exceptions**: handle them.
- A correct program should never pass a negative age to a function that forbids it, index past the end of its own list, or call a method on an object in the wrong state. These are **errors**: throw them to flag the bug, and fix the code that caused it.

### Design guidelines

- **Throw `Exception` subclasses for conditions callers are expected to handle.**
- **Throw `Error` subclasses (like `ArgumentError`) when a caller breaks your function's rules.**
- **Do not catch `Error` to "keep going".** If a bug has happened, the program state may be corrupt. The exception is a top-level handler that reports the crash (see Zones in Lesson 34).
- `Exception` is an **interface**. Create your own with `implements Exception` (29.5).
- `Exception('message')` creates a basic general-purpose exception, but a custom type is easier to catch precisely.

```dart
void main() {
  var e = Exception('Something happened');
  print(e);           // Exception: Something happened
}
```

---

## 29.5 Creating Custom Exceptions

Built-in types are generic. A custom exception gives your code its own vocabulary, so callers can catch exactly what you throw and read **structured data** instead of parsing a message string.

### A simple custom exception

```dart
class InsufficientFundsException implements Exception {
  final double balance;
  final double requested;

  const InsufficientFundsException(this.balance, this.requested);

  double get shortfall => requested - balance;

  @override
  String toString() =>
      'InsufficientFundsException: need $requested but balance is $balance';
}

class Account {
  double _balance;
  Account(this._balance);

  void withdraw(double amount) {
    if (amount > _balance) {
      throw InsufficientFundsException(_balance, amount);
    }
    _balance -= amount;
  }
}

void main() {
  var account = Account(50);

  try {
    account.withdraw(80);
  } on InsufficientFundsException catch (e) {
    print('Short by ${e.shortfall}');   // Short by 30.0
  }
}
```

### Guidelines for custom exceptions

- Name the class so it ends with `Exception`.
- Use `implements Exception` (a custom type that **extends `Error`** is for bugs only).
- Make fields `final` and the constructor `const` where possible. Exceptions should be immutable (Lesson 28).
- Carry useful data: an error code, the offending value, a `cause`.
- Override `toString` so uncaught exceptions print something readable.
- Document what you throw: `/// Throws [InsufficientFundsException] if ...`.

### A hierarchy with a sealed base class

You can organize related failures under one parent. Combined with `sealed` (Lesson 25), the compiler can check that you handle every case:

```dart
sealed class AppException implements Exception {
  final String message;
  const AppException(this.message);

  @override
  String toString() => '$runtimeType: $message';
}

final class NetworkException extends AppException {
  final int? statusCode;
  const NetworkException(super.message, {this.statusCode});
}

final class ValidationException extends AppException {
  final String field;
  const ValidationException(super.message, this.field);
}

final class NotFoundException extends AppException {
  const NotFoundException(super.message);
}

String describe(AppException e) => switch (e) {
      NetworkException(statusCode: var code) => 'Network problem (status $code)',
      ValidationException(field: var f) => 'Check the "$f" field',
      NotFoundException() => 'Nothing found',
    };

void main() {
  try {
    throw const ValidationException('Too short', 'username');
  } on AppException catch (e) {
    print(describe(e));   // Check the "username" field
  }
}
```

Catching the parent (`on AppException`) handles the whole family, and the exhaustive `switch` reminds you to update the handler whenever a new subtype is added.

### Wrapping a cause

Low-level exceptions usually mean nothing to the person using your API. Wrap them while keeping the original for diagnosis:

```dart
class ConfigException implements Exception {
  final String message;
  final Object? cause;
  const ConfigException(this.message, {this.cause});

  @override
  String toString() =>
      cause == null ? 'ConfigException: $message' : 'ConfigException: $message ($cause)';
}

int readPort(String text) {
  try {
    return int.parse(text);
  } on FormatException catch (e) {
    throw ConfigException('Invalid port "$text"', cause: e);
  }
}
```

### When not to use exceptions

Exceptions are for **exceptional** situations. If failure is a routine outcome (a search that finds nothing, a parse of user text that is often wrong), prefer returning `null`, a `tryParse`-style method, or a result type (Lesson 30). It makes the possibility of failure visible in the function's signature, and it is easier on performance.

---

[Previous](./[28]-Equality-and-Immutability.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[30]-Assertions.md)
