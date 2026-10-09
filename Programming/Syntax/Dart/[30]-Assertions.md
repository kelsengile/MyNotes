[Previous](./[29]-Exceptions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[31]-Futures-Async-Await.md)

*Error Handling*

# Lesson 30 - Assertions & Defensive Programming

Exceptions (Lesson 29) deal with things going wrong while a program runs. **Defensive programming** is the habit of writing code that protects itself: it states what must be true, checks inputs at the right places, and makes failures obvious and early instead of letting bad data travel deeper into the program. This lesson covers three tools: `assert` for catching your own mistakes during development, input validation for guarding your public functions, and result types for modeling failures that are part of normal life.

---

## 30.1 `assert` and Debug Mode

An **assertion** is a statement that says "this must be true here." If it is not, the program stops with an `AssertionError`. Assertions are a development aid: they catch **bugs in your own logic** early, with a clear message.

```dart
void setVolume(int level) {
  assert(level >= 0 && level <= 100, 'level must be between 0 and 100, got $level');
  print('Volume set to $level');
}

void main() {
  setVolume(50);     // Volume set to 50
  setVolume(150);    // AssertionError (when assertions are enabled)
}
```

Syntax: `assert(condition);` or `assert(condition, 'message');`. The condition must be a `bool`. The optional message can be any object (usually a string) and appears in the error output.

### Assertions only run in debug mode

This is the most important fact about `assert`:

- When assertions are **enabled**, a false condition throws `AssertionError`.
- When assertions are **disabled**, Dart ignores the statement completely. The condition is **not even evaluated**.

| Environment | Assertions |
|---|---|
| Flutter debug builds (and `flutter test`) | Enabled |
| Flutter profile and release builds | Disabled |
| Dart command line (`dart run`, `dart test`) | Controlled by the `--enable-asserts` flag; use it explicitly to be sure |
| Compiled programs (`dart compile exe`, `dart compile js` for release) | Disabled unless the compile option enables them |
| Development servers for the web (such as `webdev serve`) | Usually enabled |

```
dart run --enable-asserts bin/main.dart
```

Because assertions can disappear in production, follow these rules:

1. **Never put required logic or side effects inside an `assert`.** If the code only runs in debug mode, your release app behaves differently:

```dart
assert(items.removeLast() != null);   // BAD: removes an item only when asserts are on

// Good: do the work normally, then assert on the result
var last = items.removeLast();
assert(last != null);
```

2. **Do not use `assert` to validate input from users, files, or networks.** Bad input still arrives in production, where the check would vanish. Use real validation (30.2).
3. **Use `assert` for conditions that should be impossible if your own code is correct.** It documents your assumptions and catches mistakes while testing.

### Where you can use `assert`

**In function bodies** (as above) and **in initializer lists**, where it runs before the constructor body (Lesson 21):

```dart
class Circle {
  final double radius;

  Circle(this.radius) : assert(radius >= 0, 'radius cannot be negative');
}

class Range {
  final int min;
  final int max;

  const Range(this.min, this.max) : assert(min <= max, 'min must not exceed max');
}
```

In a `const` constructor, a failing assertion on a `const` object is reported at **compile time**, even without any flag:

```dart
const ok = Range(1, 5);
// const bad = Range(9, 2);    // compile-time error: assertion failed
```

### Running debug-only code

A well-known trick is to put debug-only code inside an assertion that always returns `true`. The closure runs only when assertions are enabled:

```dart
void main() {
  assert(() {
    print('Debug mode: extra checks are running');
    return true;
  }());

  print('Program started');
}
```

### Catching `AssertionError`

`AssertionError` is an `Error` (Lesson 29), so it signals a bug. Do not catch it to continue; fix the code that violated the assertion. In tests it is common to check that a bad value triggers it.

### `assert` vs `throw`

| | `assert` | `throw` |
|---|---|---|
| Runs in production | No (normally) | Yes |
| Meant for | Your own logic errors | Invalid input and recoverable problems |
| Cost in release builds | None | The check always runs |
| Typical error | `AssertionError` | `ArgumentError`, `StateError`, custom exceptions |

---

## 30.2 Validating Inputs

**Validation** means checking data before you trust it. Do it at the **boundaries** of your program or module: where data enters from users, files, networks, or other libraries. Once data is checked, the code behind that boundary can safely assume it is valid.

### Guard clauses: check first, fail fast

Put checks at the **top** of a function and exit (or throw) immediately. This keeps the main logic uncluttered and the error close to its cause:

```dart
double divide(num a, num b) {
  if (b == 0) {
    throw ArgumentError.value(b, 'b', 'must not be zero');
  }
  return a / b;
}

String greet(String name) {
  if (name.trim().isEmpty) {
    throw ArgumentError('Name must not be empty');
  }
  return 'Hello, ${name.trim()}!';
}
```

### Choosing the right error to throw

| Situation | Throw |
|---|---|
| An argument has a bad value | `ArgumentError` or `ArgumentError.value(value, 'name', 'message')` |
| An index or number is outside its range | `RangeError` or `RangeError.range(value, min, max, 'name')` |
| The object is not in the right state for this call | `StateError('message')` |
| The operation is not supported by this type | `UnsupportedError('message')` |
| Text has a bad format | `FormatException('message', source)` |
| A domain rule is broken that callers might handle | A custom exception (Lesson 29) |

```dart
class Thermostat {
  double _target = 20;
  bool _on = false;

  void turnOn() => _on = true;

  void setTarget(double celsius) {
    if (!_on) {
      throw StateError('Turn the thermostat on before setting a target');
    }
    RangeError.checkValueInInterval(celsius.round(), 10, 30, 'celsius');
    _target = celsius;
  }
}
```

### Validation functions that report problems

When you are validating user input (a form, for instance), throwing on the first mistake is not friendly. Return a message instead, or collect all of them. The validator shape `String? Function(String)` from Lesson 19 is perfect: it returns an error message, or `null` when the input is valid.

```dart
String? validateUsername(String input) {
  if (input.isEmpty) return 'Username is required';
  if (input.length < 3) return 'Use at least 3 characters';
  if (!RegExp(r'^[a-z0-9_]+$').hasMatch(input)) {
    return 'Use only lowercase letters, digits, and underscores';
  }
  return null;     // valid
}

List<String> validateSignup(String username, String password) {
  final problems = <String>[];

  final nameError = validateUsername(username);
  if (nameError != null) problems.add(nameError);

  if (password.length < 8) problems.add('Password needs 8 or more characters');

  return problems;
}

void main() {
  print(validateSignup('Al', 'abc'));
  // [Use at least 3 characters, Password needs 8 or more characters]
  print(validateSignup('ana_21', 'longenough'));   // []
}
```

### Parse, don't just validate

Checking a value and then continuing to pass around the raw `String` or `int` means every later function must wonder "was this checked?" A stronger technique is to **convert** the data into a type that can only exist when valid. Extension types (Lesson 26) and small classes are ideal:

```dart
extension type Email._(String value) {
  Email(String input) : value = input.trim().toLowerCase() {
    if (!value.contains('@')) {
      throw FormatException('Not an email address', input);
    }
  }
}

void sendWelcome(Email to) => print('Sending welcome to ${to.value}');
```

Any function that accepts an `Email` can trust it without re-checking. Illegal values never get past the boundary.

### More ways to make bad states impossible

- **Use the type system.** Non-nullable types (Lesson 8) remove entire categories of checks. Enums (Lesson 18) restrict a value to a known set. Sealed classes (Lesson 25) enumerate every case.
- **Prefer `final` and immutable objects** (Lesson 28) so a validated value cannot become invalid later.
- **Use `required` named parameters** so callers cannot forget something important.
- **Provide safe alternatives:** `int.tryParse` instead of `int.parse`, `firstOrNull` instead of `first` on a possibly empty list.
- **Validate once, at the boundary,** not in every function.

### Defensive programming checklist

- Assume input is wrong until proven otherwise.
- Check early and fail loudly with a **clear message** that includes the offending value.
- Do not swallow exceptions: an empty `catch {}` hides bugs.
- Do not return "magic" values like `-1` or an empty string to signal failure; use `null`, an exception, or a result type.
- Document what your public functions throw.
- Use `assert` to state assumptions that **your own code** must keep true.

---

## 30.3 Result-Style Error Handling with Sealed Classes

Exceptions are invisible: nothing in a function's signature says it might throw. For failures that are **ordinary outcomes** (a login with the wrong password, a parse of user text, a search that finds nothing) you can model success and failure as **values** that the caller is forced to look at. This is called **result-style** error handling.

With a sealed class (Lesson 25) the compiler knows every possible case, so a `switch` that forgets the failure branch is a compile-time error.

### A `Result` type

```dart
sealed class Result<T, E> {
  const Result();

  /// Transforms the value if this is a success; passes a failure through unchanged.
  Result<R, E> map<R>(R Function(T value) transform) => switch (this) {
        Success<T, E>(:final value) => Success<R, E>(transform(value)),
        Failure<T, E>(:final error) => Failure<R, E>(error),
      };

  /// The value if this is a success, otherwise the fallback.
  T getOrElse(T fallback) => switch (this) {
        Success<T, E>(:final value) => value,
        Failure<T, E>() => fallback,
      };
}

final class Success<T, E> extends Result<T, E> {
  final T value;
  const Success(this.value);
}

final class Failure<T, E> extends Result<T, E> {
  final E error;
  const Failure(this.error);
}
```

`T` is the type of the success value and `E` is the type of the error (generics, Lesson 27). `Success` and `Failure` are the only two subtypes, and they live in the same library as the sealed parent.

### Producing results

```dart
Result<int, String> parseAge(String text) {
  final age = int.tryParse(text);
  if (age == null) return Failure('"$text" is not a number');
  if (age < 0 || age > 150) return Failure('Age out of range: $age');
  return Success(age);
}
```

The return type `Result<int, String>` tells every caller, in the signature itself, that this operation can fail and how.

### Consuming results

The caller must handle both cases. A `switch` over the sealed type checks exhaustiveness for you:

```dart
void main() {
  for (final input in ['25', 'abc', '-4']) {
    final result = parseAge(input);

    switch (result) {
      case Success(:final value):
        print('Valid age: $value');
      case Failure(:final error):
        print('Invalid: $error');
    }
  }
  // Valid age: 25
  // Invalid: "abc" is not a number
  // Invalid: Age out of range: -4
}
```

A `switch` expression works too, and helper methods keep call sites short:

```dart
void main() {
  final message = switch (parseAge('30')) {
    Success(:final value) => 'Next year you will be ${value + 1}',
    Failure(:final error) => 'Could not read age: $error',
  };
  print(message);   // Next year you will be 31

  print(parseAge('x').getOrElse(18));             // 18
  print(parseAge('40').map((age) => age * 12).getOrElse(0));   // 480
}
```

### Structured errors

The error type does not have to be a string. Use a sealed hierarchy of errors so that callers can react to each cause, again with exhaustiveness checking:

```dart
sealed class LoginError {}

class UserNotFound extends LoginError {
  final String username;
  UserNotFound(this.username);
}

class WrongPassword extends LoginError {}

class AccountLocked extends LoginError {
  final Duration retryAfter;
  AccountLocked(this.retryAfter);
}

Result<String, LoginError> login(String user, String password) {
  if (user != 'ana') return Failure(UserNotFound(user));
  if (password != 'secret') return Failure(WrongPassword());
  return Success('token-123');
}

String explain(LoginError error) => switch (error) {
      UserNotFound(:final username) => 'No account named "$username"',
      WrongPassword() => 'Wrong password',
      AccountLocked(:final retryAfter) =>
        'Locked. Try again in ${retryAfter.inMinutes} minutes',
    };

void main() {
  switch (login('ana', 'oops')) {
    case Success(:final value):
      print('Logged in with $value');
    case Failure(:final error):
      print(explain(error));   // Wrong password
  }
}
```

If you later add a new `LoginError` subtype, every `switch` that handles errors stops compiling until you handle it. That is the safety net exceptions cannot give you.

### Exceptions or results?

| Use **exceptions** when... | Use **results** when... |
|---|---|
| The failure is unexpected or exceptional | The failure is a normal, expected outcome |
| The error should travel far up the call stack | The caller should deal with it immediately |
| The problem is a bug or broken precondition | You want the compiler to force callers to handle failure |
| You are working with libraries that already throw | Failure carries structured data the caller will branch on |

Many real programs combine the two: exceptions for programming errors and truly exceptional conditions, results for expected business-rule failures. Whichever you choose, be consistent within a module so callers know what to expect.

Other lightweight options exist too: returning `null` for "nothing found", a record such as `(value: 5, error: null)`, or the `Result` types provided by packages. The sealed-class approach above needs no extra dependency and works naturally with Dart's pattern matching.

---

[Previous](./[29]-Exceptions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[31]-Futures-Async-Await.md)
