[Previous](./[7]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[9]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 8 - Null Safety

`null` means "no value". In many languages, accidentally using `null` causes the famous "null pointer" crash at run time. Dart's **sound null safety** moves that problem to compile time: the analyzer tells you about a possible `null` mistake before the program ever runs. This lesson explains how it works and the operators that make it comfortable.

---

## 8.1 Nullable vs Non-Nullable Types

In Dart, types are **non-nullable by default**. A variable of type `String` can never hold `null`:

```dart
String name = 'Ana';
// name = null;   // ERROR: 'Null' can't be assigned to 'String'
```

To allow `null`, add a question mark to the type. `String?` means "either a `String` or `null`":

```dart
String? nickname;          // starts as null
print(nickname);           // null

nickname = 'Annie';
print(nickname);           // Annie

nickname = null;           // allowed
```

Every type has a nullable twin: `int` / `int?`, `double` / `double?`, `List<int>` / `List<int>?`, and so on.

### What you can do with a nullable value

The compiler stops you from using a nullable value as if it were definitely there:

```dart
String? text = 'hello';
// print(text.length);   // ERROR: 'length' can't be accessed on 'String?'
//                       // because the receiver can be 'null'
```

You must first deal with the `null` possibility. The rest of this lesson covers the ways to do that.

A few members work on every value, including `null`, because `null` is an object of type `Null`: `toString()`, `==`, `hashCode`, and `runtimeType`.

```dart
String? text;
print(text.toString());  // "null"
print(text == null);     // true
```

### Where nulls appear in practice

Some standard library calls naturally return nullable types:

```dart
var scores = {'ana': 90, 'ben': 75};
int? s = scores['carl'];            // Map lookups return V? (null if missing)

int? n = int.tryParse('abc');       // null when parsing fails

var list = <int>[];
int? first = list.firstOrNull;      // null if the list is empty
```

---

## 8.2 The Null-Aware Operators

Dart provides several short operators for working with nullable values.

### `??` (if-null)

`a ?? b` gives `a` if it is not null, otherwise `b`:

```dart
String? name;
print(name ?? 'Guest');     // Guest

name = 'Ana';
print(name ?? 'Guest');     // Ana
```

### `?.` (null-aware access)

`a?.b` accesses `b` only if `a` is not null; otherwise the whole expression is `null`:

```dart
String? text;
print(text?.length);        // null (no crash)

text = 'hello';
print(text?.length);        // 5
```

The result type is nullable (`int?` here). Combine it with `??` to supply a default:

```dart
String? text;
int length = text?.length ?? 0;
print(length);              // 0
```

The `?.` operator also **short-circuits** a whole chain: if the left side is null, the rest of the chain is skipped.

```dart
String? text;
print(text?.trim().toUpperCase().length);  // null; trim() etc. never run
```

### `??=` (null-aware assignment)

`a ??= b` assigns `b` to `a` only if `a` is currently null:

```dart
int? cache;
cache ??= 42;     // cache was null, so it becomes 42
cache ??= 99;     // cache is not null, so nothing happens
print(cache);     // 42
```

### Other null-aware forms

| Syntax | Meaning |
|---|---|
| `list?[0]` | Null-aware index: `null` if `list` is null |
| `a?..b = 1` | Null-aware cascade (see Lesson 9) |
| `[...?other]` | Null-aware spread: adds nothing if `other` is null (see Lesson 15) |

```dart
List<int>? numbers;
print(numbers?[0]);          // null

numbers = [10, 20];
print(numbers?[0]);          // 10
```

---

## 8.3 The Null Assertion Operator (`!`)

Putting `!` after an expression says to the compiler, "I know this is not null, trust me." The static type becomes non-nullable:

```dart
String? maybe = 'hello';
String sure = maybe!;        // OK: treated as String
print(sure.length);          // 5
```

If you are wrong, the program throws a run-time error:

```dart
String? nothing;
// String oops = nothing!;   // Runtime error: Null check operator used on a null value
```

So `!` brings back the very crash null safety tries to prevent. Use it **sparingly**, and only when you can truly guarantee the value is not null. Better alternatives:

```dart
String? input = readInput();

// Option 1: provide a default
var a = input ?? 'default';

// Option 2: check first
if (input != null) {
  print(input.length);   // promoted to String: see 8.4
}

// Option 3: bail out early
if (input == null) return;
print(input.length);     // also promoted
```

(`readInput()` above stands in for any function returning `String?`.)

---

## 8.4 Type Promotion and Flow Analysis

Dart's **flow analysis** tracks what your code has checked. After a null check, the compiler **promotes** the variable from `String?` to `String` inside the region where it is known to be non-null:

```dart
int lengthOf(String? text) {
  if (text == null) {
    return 0;
  }
  // From here on, 'text' is known to be a non-null String
  return text.length;
}
```

Promotion works with several shapes of check:

```dart
void demo(String? a, Object b) {
  // 1. if with != null
  if (a != null) {
    print(a.length);          // a is String
  }

  // 2. early exit
  if (a == null) return;
  print(a.length);            // a is String for the rest of the function

  // 3. type tests also promote
  if (b is String) {
    print(b.toUpperCase());   // b is String
  }

  // 4. combining conditions with && and ||
  if (a.isNotEmpty && b is int) {
    print(b + 1);             // b is int
  }
}
```

### When promotion does *not* happen

Promotion only works when the compiler can be sure the value cannot change between the check and the use. That works well for **local variables** and parameters, but not for ordinary class fields, because another part of the program might change them in between.

```dart
class User {
  String? name;                 // a public, mutable field

  void greet() {
    if (name != null) {
      // print(name.length);    // ERROR: fields are not promoted
      print(name!.length);      // works, but uses '!'
    }
  }
}
```

Two common fixes:

```dart
class User {
  String? name;

  void greet() {
    // Fix 1: copy into a local variable, then check the local
    final n = name;
    if (n != null) {
      print(n.length);          // promoted
    }
  }
}

class Account {
  // Fix 2: private final fields CAN be promoted (Dart 3.2 and later)
  final String? _owner;
  Account(this._owner);

  void show() {
    if (_owner != null) {
      print(_owner.length);     // promoted
    }
  }
}
```

---

## 8.5 `late` and Null Safety

Sometimes a non-nullable variable cannot be given a value at the moment it is declared, but you know it will be set before it is used. `late` (introduced in Lesson 5) handles this without making the type nullable:

```dart
class Report {
  late String title;        // non-nullable, assigned later

  void init() {
    title = 'Monthly Report';
  }
}
```

Compare the two approaches:

| Approach | Reading before assignment | Using it |
|---|---|---|
| `String? title` | Gives `null` | Must null-check every time |
| `late String title` | Throws `LateInitializationError` | Use directly, no checks |

A `late` variable is a trade: you skip the null checks, but the compiler stops guarding you. Use it when initialization order is guaranteed (for example, a value created in a setup method that always runs first), and use a nullable type when "not set yet" is a legitimate state of your data.

`late` with an initializer is also handy for expensive values you want to compute only when needed (see Lesson 5.4).

---

## 8.6 Migrating from Legacy Code

Null safety arrived in Dart 2.12. Today (Dart 3 and later), **every Dart program is sound null safe**, and the old "unsound" mode has been removed. If you find older code or tutorials written before null safety, you will notice:

- Types had no `?`; any variable could silently hold `null`.
- The SDK constraint in `pubspec.yaml` was below 2.12, such as `sdk: '>=2.7.0 <3.0.0'`.
- Code used `// @dart=2.9` comments to opt out of null safety.

To bring old code up to date:

1. Update the SDK constraint in `pubspec.yaml` to a modern version, for example `sdk: ^3.0.0`.
2. Remove any `// @dart=2.9` comments.
3. Run `dart analyze` and fix each error: add `?` to types that can legitimately be null, add `required` to named parameters that must be passed (Lesson 12), and initialize or mark fields `late`.
4. Update dependencies to versions that support null safety (`dart pub upgrade --major-versions`).

The automated `dart migrate` tool was part of Dart 2.12 to 2.19 and no longer ships with Dart 3. For a large legacy project, migrate it using one of those older SDK versions first, then upgrade.

A summary of the key tools in this lesson:

| Tool | Syntax | Purpose |
|---|---|---|
| Nullable type | `T?` | Allow `null` |
| If-null | `a ?? b` | Default value |
| Null-aware access | `a?.b` | Safe member access |
| Null-aware assign | `a ??= b` | Assign only if null |
| Null assertion | `a!` | Force non-null (risky) |
| Promotion | `if (a != null)` | Compiler-verified non-null |

---

[Previous](./[7]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[9]-Operators-and-Expressions.md)
