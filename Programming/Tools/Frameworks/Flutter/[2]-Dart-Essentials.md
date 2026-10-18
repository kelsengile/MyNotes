[Previous](./[1]-What-Is-Flutter.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[3]-Widgets-Fundamentals.md)

*Basics*

# Lesson 2 - Dart Essentials

Flutter apps are written in Dart, a statically typed, object-oriented language with a syntax close to Java, C#, and JavaScript. This lesson covers the parts of Dart you need before building widgets.

---

## 2.1 Variables and null safety

```dart
var name = 'Ada';          // type inferred: String
String city = 'London';    // explicit type
final age = 36;            // set once at runtime
const pi = 3.14159;        // compile-time constant
int count = 0;
double price = 9.99;
bool isOpen = true;
```

- `var` lets the compiler infer the type. The type is fixed once inferred.
- `final` can be assigned exactly once, and the value may be computed at runtime.
- `const` must be known at compile time. Flutter uses `const` widgets heavily to skip needless rebuilds.

**Null safety.** By default a type cannot hold `null`. Add `?` to allow it:

```dart
String title = 'Hello';
// title = null;          // compile error

String? nickname;          // may be null, starts as null

print(nickname?.length);   // null-aware access: null if nickname is null
print(nickname ?? 'none'); // default value if null
nickname ??= 'Bob';        // assign only if currently null
print(nickname!.length);   // "!" asserts non-null; throws if wrong
```

Avoid `!` unless you are certain, because it moves the error from compile time to runtime. Dart also promotes types after a null check:

```dart
void greet(String? name) {
  if (name == null) return;
  print(name.toUpperCase()); // name is a non-null String here
}
```

`late` delays initialization of a non-null variable: `late String token;`

**String interpolation:** `'Hello $name, you are ${age + 1} next year'`

---

## 2.2 Functions and classes

**Functions:**

```dart
int add(int a, int b) => a + b;     // arrow syntax for one expression

void log(String message) {
  print(message);
}
```

**Named and optional parameters.** Flutter widgets use named parameters constantly:

```dart
void createUser({required String name, int age = 18, String? email}) {
  print('$name, $age, $email');
}

createUser(name: 'Ada');
createUser(name: 'Bob', age: 25, email: 'bob@example.com');
```

- `{ }` makes parameters named.
- `required` forces the caller to supply them.
- `[ ]` would make positional parameters optional instead.

**Classes:**

```dart
class Dog {
  final String name;
  int age;

  Dog(this.name, this.age);              // shorthand constructor

  Dog.puppy(this.name) : age = 0;        // named constructor

  void bark() => print('$name says woof');

  String get description => '$name ($age)'; // getter
}

void main() {
  final d = Dog('Rex', 3);
  d.bark();
  print(Dog.puppy('Milo').description);
}
```

**Inheritance, `extends` and `super`:**

```dart
class Animal {
  void speak() => print('...');
}

class Cat extends Animal {
  @override
  void speak() => print('Meow');
}
```

Dart also has `abstract` classes, `mixin`s (`with`), and `enum`s, which you will meet in Flutter code. Members whose names start with `_` are private to their file.

---

## 2.3 Collections

```dart
// List
final fruits = <String>['apple', 'banana'];
fruits.add('cherry');
print(fruits[0]);        // apple
print(fruits.length);    // 3

// Set: unique values
final ids = <int>{1, 2, 3};

// Map: key-value pairs
final ages = <String, int>{'Ada': 36, 'Bob': 25};
ages['Cy'] = 30;
print(ages['Ada']);      // 36 (type int?, since the key might be missing)
```

**Useful operations:**

```dart
final nums = [1, 2, 3, 4, 5];

final doubled = nums.map((n) => n * 2).toList();   // [2, 4, 6, 8, 10]
final evens = nums.where((n) => n.isEven).toList(); // [2, 4]
final total = nums.fold(0, (sum, n) => sum + n);    // 15

for (final n in nums) {
  print(n);
}
```

**Collection `if` and `for` and the spread operator.** These are very common inside widget lists:

```dart
final showExtra = true;
final items = [
  'A',
  'B',
  if (showExtra) 'Extra',
  for (var i = 1; i <= 2; i++) 'Item $i',
  ...fruits,               // spread another list in
];
```

---

## 2.4 Futures, async, and await

Many operations take time: network calls, file reads, database queries. Dart models these with a **`Future<T>`**, a value that will be available later.

```dart
Future<String> fetchGreeting() async {
  await Future.delayed(const Duration(seconds: 2)); // simulate work
  return 'Hello from the future';
}

Future<void> main() async {
  print('Start');
  final msg = await fetchGreeting(); // pause here without blocking the UI
  print(msg);
  print('End');
}
```

- `async` marks a function that uses `await` and returns a `Future`.
- `await` waits for a `Future` to complete and gives you its value.
- Dart is single-threaded per isolate, but `await` lets other work (like drawing frames) continue while you wait.

**Error handling:**

```dart
try {
  final msg = await fetchGreeting();
  print(msg);
} catch (e) {
  print('Failed: $e');
} finally {
  print('Done');
}
```

**Streams** deliver many values over time rather than one:

```dart
Stream<int> countDown() async* {
  for (var i = 3; i > 0; i--) {
    await Future.delayed(const Duration(seconds: 1));
    yield i;
  }
}

await for (final n in countDown()) {
  print(n);
}
```

You will meet `Future` in networking (Lesson 12) and `Stream` in state management and Firebase.

---

[Previous](./[1]-What-Is-Flutter.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[3]-Widgets-Fundamentals.md)