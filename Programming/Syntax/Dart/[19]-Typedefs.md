
[Previous](./[18]-Enums.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[20]-Classes-and-Objects.md)

*Core Syntax*

# Lesson 19 - Typedefs & Type Aliases

Some types are long or hard to read, such as `Map<String, List<Map<String, dynamic>>>` or `void Function(String, int)`. A **typedef** (type alias) gives such a type a short, meaningful name. It does not create a new type; it is simply a nickname that makes code easier to read and change.

---

## 19.1 Function Type Aliases

Function types are the original reason typedefs exist. Instead of repeating a long function type everywhere, name it once:

```dart
typedef IntOperation = int Function(int a, int b);

int add(int a, int b) => a + b;
int multiply(int a, int b) => a * b;

int calculate(IntOperation op, int x, int y) => op(x, y);

void main() {
  print(calculate(add, 3, 4));        // 7
  print(calculate(multiply, 3, 4));   // 12
  print(calculate((a, b) => a - b, 10, 4));   // 6
}
```

Syntax: `typedef Name = ReturnType Function(ParameterTypes);`

More examples:

```dart
typedef Predicate = bool Function(int value);
typedef VoidCallback = void Function();
typedef Formatter = String Function(Object? value);
typedef Validator = String? Function(String input);   // returns an error message or null
```

Using them in fields and parameters:

```dart
typedef ClickHandler = void Function(String buttonName);

class Button {
  final String name;
  final ClickHandler onClick;

  Button(this.name, this.onClick);

  void press() => onClick(name);
}

void main() {
  var b = Button('OK', (n) => print('$n pressed'));
  b.press();   // OK pressed
}
```

Named and optional parameters can be part of the function type:

```dart
typedef Logger = void Function(String message, {int level, bool timestamp});
typedef Maker = String Function(String name, [int? age]);
```

### Typedefs are just aliases

A typedef does not create a new, incompatible type. Any function with the right shape works, and the alias and the original are interchangeable:

```dart
typedef Callback = void Function(int);

void run(void Function(int) f) => f(1);   // takes the full function type
void main() {
  Callback cb = (n) => print(n);
  run(cb);   // OK: Callback and void Function(int) are the same type
}
```

### The old syntax

Older Dart code used a different, now-discouraged form: `typedef int Compare(int a, int b);`. You may meet it in legacy code. Prefer the `typedef Name = ...` form, which works for every kind of type.

---

## 19.2 Generic Type Aliases

A typedef can have **type parameters**, just like a class or function. This is useful for shortening generic types.

```dart
typedef StringMap<V> = Map<String, V>;
typedef Pair<T> = (T, T);
typedef Transformer<T, R> = R Function(T input);

void main() {
  StringMap<int> ages = {'Ana': 21, 'Ben': 19};   // a Map<String, int>
  Pair<double> point = (1.5, 2.5);                // a (double, double) record

  Transformer<String, int> length = (s) => s.length;
  print(length('hello'));   // 5
  print(ages);              // {Ana: 21, Ben: 19}
  print(point);             // (1.5, 2.5)
}
```

Since Dart 2.13, a typedef can alias **any** type, not only function types: classes, generics, records, even `dynamic`-based structures.

### Constructors through an alias

If an alias points to a class, you can call that class's constructors using the alias name:

```dart
typedef IntList = List<int>;

void main() {
  var zeros = IntList.filled(3, 0);   // same as List<int>.filled(3, 0)
  print(zeros);                       // [0, 0, 0]
}
```

### Bounds on alias parameters

```dart
typedef NumberList<T extends num> = List<T>;

NumberList<int> evens = [2, 4, 6];
// NumberList<String> bad = [];   // ERROR: String does not extend num
```

---

## 19.3 Using Aliases for Readability

Aliases are most valuable when a type is long, repeated, or has a domain meaning.

### Shorten JSON-style types

```dart
typedef Json = Map<String, dynamic>;
typedef JsonList = List<Json>;

Json user = {'name': 'Ana', 'tags': ['a', 'b']};

String nameOf(Json data) => data['name'] as String;
```

Compare `Map<String, dynamic>` repeated fifty times with `Json`.

### Give meaning to simple types

```dart
typedef UserId = int;
typedef Email = String;

void sendWelcome(UserId id, Email address) {
  print('Welcome #$id at $address');
}
```

This documents the code, but remember that a typedef is **only a nickname**: the compiler treats `UserId` and plain `int` as the same type, so it will not stop you from passing an order number where a user ID is expected. If you need the compiler to enforce the difference, use an **extension type** (Lesson 26) or a small class.

### Name records

Records have no name of their own, so an alias gives them one:

```dart
typedef Coordinates = ({double lat, double lng});

Coordinates manila = (lat: 14.5995, lng: 120.9842);

String describe(Coordinates c) => '${c.lat}, ${c.lng}';
```

### Name callbacks

```dart
typedef AsyncTask<T> = Future<T> Function();
typedef EventListener<E> = void Function(E event);
```

### Guidelines

| Do | Avoid |
|---|---|
| Alias types you repeat or that are hard to read | Aliasing short types like `int` for no reason |
| Pick a name that explains the *role* | Believing an alias creates a distinct, checked type |
| Keep aliases near the code that uses them, or export them from a library for shared use | Hiding important structure; use a class when the data deserves behavior |

---

[Previous](./[18]-Enums.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[20]-Classes-and-Objects.md)