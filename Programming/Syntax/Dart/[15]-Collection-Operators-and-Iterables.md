[Previous](./[14]-Sets-and-Maps.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[16]-Records.md)

*Core Syntax*

# Lesson 15 - Collection Operators & Iterables

Dart has special syntax for *building* collections from other collections, and a family of methods for *transforming* them. Together they let you express in one line what would take a loop and several statements in other languages. This lesson also explains the idea behind all of them: the `Iterable`.

---

## 15.1 The Spread Operators (`...` and `...?`)

The **spread operator** `...` inserts all the elements of one collection into another collection literal.

```dart
void main() {
  var first = [1, 2, 3];
  var second = [4, 5];

  var combined = [...first, ...second];
  print(combined);              // [1, 2, 3, 4, 5]

  var withExtras = [0, ...first, 99, ...second, 100];
  print(withExtras);            // [0, 1, 2, 3, 99, 4, 5, 100]
}
```

It also makes a quick **copy** of a collection, and it works with sets and maps:

```dart
void main() {
  var original = [1, 2, 3];
  var copy = [...original];     // independent copy

  var a = {1, 2};
  var b = {2, 3};
  print({...a, ...b});          // {1, 2, 3}

  var defaults = {'theme': 'light', 'size': 12};
  var custom = {'size': 16};
  print({...defaults, ...custom});  // {theme: light, size: 16}  later entries win
}
```

Merging maps like this is a neat way to apply overrides to default settings.

### Null-aware spread `...?`

Spreading `null` is an error. If the collection might be null, use `...?`, which adds nothing when the value is null:

```dart
void main() {
  List<int>? maybe;                 // null

  var list = [1, 2, ...?maybe];     // OK: nothing added
  print(list);                      // [1, 2]

  maybe = [3, 4];
  print([1, 2, ...?maybe]);         // [1, 2, 3, 4]
}
```

You can spread any `Iterable`, not just lists (for example `...set` or `...map.keys`).

---

## 15.2 Collection `if` and Collection `for`

Dart lets you put `if` and `for` **directly inside a collection literal** to decide which elements to include or to generate elements.

### Collection `if`

```dart
void main() {
  var isLoggedIn = true;
  var isAdmin = false;

  var menu = [
    'Home',
    'Products',
    if (isLoggedIn) 'Profile',
    if (isAdmin) 'Admin Panel',
  ];

  print(menu);   // [Home, Products, Profile]
}
```

It also supports `else`:

```dart
var temperature = 15;
var advice = [
  'Drink water',
  if (temperature > 25) 'Wear sunscreen' else 'Bring a jacket',
];
print(advice);   // [Drink water, Bring a jacket]
```

An `if` can also use a pattern with `case`:

```dart
Object value = 5;
var items = [
  'start',
  if (value case int n when n > 3) 'big number $n',
];
print(items);  // [start, big number 5]
```

### Collection `for`

```dart
void main() {
  var numbers = [1, 2, 3];

  var doubled = [for (var n in numbers) n * 2];
  print(doubled);   // [2, 4, 6]

  var squares = [for (var i = 1; i <= 5; i++) i * i];
  print(squares);   // [1, 4, 9, 16, 25]

  // Combine for with if to filter
  var evens = [for (var i = 1; i <= 10; i++) if (i.isEven) i];
  print(evens);     // [2, 4, 6, 8, 10]
}
```

### Using them together, and with sets and maps

They work in sets and maps too, and can be mixed with spread:

```dart
void main() {
  var names = ['ana', 'ben'];

  var lookup = {for (var n in names) n: n.length};
  print(lookup);    // {ana: 3, ben: 3}

  var letters = {for (var n in names) ...n.split('')};
  print(letters);   // {a, n, b, e}

  var nested = [
    for (var i = 1; i <= 2; i++)
      for (var j = 1; j <= 2; j++) '$i-$j',
  ];
  print(nested);    // [1-1, 1-2, 2-1, 2-2]
}
```

Collection `if` and `for` are **expressions inside the literal**, not statements. They shine when building lists in Flutter UI code, where parts of a screen appear conditionally.

---

## 15.3 `Iterable` vs `List`

An **`Iterable`** is any collection of elements that can be accessed one after another. `List` and `Set` are both iterables, and so are the `keys` and `values` of a map, and the result of methods like `map` and `where`.

A **`List`** is an `Iterable` that additionally supports:

- access by **index** (`list[3]`),
- a known `length` available instantly,
- changing its elements.

An `Iterable` on its own offers fewer capabilities, but the ones it has work on *every* kind of collection:

| Capability | `Iterable` | `List` |
|---|---|---|
| `for-in` loop | Yes | Yes |
| `map`, `where`, `fold`, `any`, `every`, `contains`, `first`, `last`, `take`, `skip` | Yes | Yes |
| `[index]` access | **No** | Yes |
| `add`, `remove` | **No** | Yes |
| Guaranteed fast `length` | Not always | Yes |

```dart
void main() {
  Iterable<int> nums = [10, 20, 30];     // a List used as an Iterable
  print(nums.first);                     // 10
  print(nums.elementAt(1));              // 20  (the Iterable way to get an item by position)
  // print(nums[1]);                     // ERROR: Iterable has no [] operator

  var evens = [1, 2, 3, 4].where((n) => n.isEven);  // type: Iterable<int>, not List<int>
  print(evens);                          // (2, 4)   note: parentheses, not brackets
  print(evens.toList());                 // [2, 4]
}
```

Printing an `Iterable` shows **parentheses**, while a list shows square brackets. If you see `(...)` in your output, you have an `Iterable`.

### Converting between them

```dart
var iterable = [1, 2, 3].map((n) => n * 2);

var asList = iterable.toList();    // List<int>
var asSet = iterable.toSet();      // Set<int>
```

### Writing flexible functions

A function that only needs to loop over items should accept an `Iterable`, so callers can pass a list, a set, or anything else:

```dart
int sum(Iterable<int> values) {
  var total = 0;
  for (final v in values) {
    total += v;
  }
  return total;
}

void main() {
  print(sum([1, 2, 3]));        // 6  (a list)
  print(sum({4, 5, 6}));        // 15 (a set)
  print(sum([1, 2, 3].map((n) => n * 10)));  // 60 (an iterable)
}
```

---

## 15.4 `map`, `where`, `reduce`, `fold`, `expand`

These methods exist on every `Iterable`. Each takes a function and produces something new without changing the original collection.

### `map`: transform every element

```dart
void main() {
  var names = ['ana', 'ben'];
  var upper = names.map((n) => n.toUpperCase());
  print(upper.toList());    // [ANA, BEN]

  var lengths = names.map((n) => n.length).toList();
  print(lengths);           // [3, 3]
}
```

The resulting element type can differ from the original (strings in, integers out).

### `where`: keep elements that pass a test

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5, 6];
  var evens = numbers.where((n) => n.isEven).toList();
  print(evens);   // [2, 4, 6]

  var mixed = [1, 'a', 2, 'b'];
  print(mixed.whereType<int>().toList());   // [1, 2]  filter by type
}
```

### `reduce`: combine all elements into one

`reduce` repeatedly combines two elements into one, starting with the first two. It returns a value of the **same type** as the elements, and **throws a `StateError` on an empty collection**:

```dart
void main() {
  var numbers = [3, 7, 2, 9];
  var total = numbers.reduce((a, b) => a + b);
  var biggest = numbers.reduce((a, b) => a > b ? a : b);
  print(total);     // 21
  print(biggest);   // 9
}
```

### `fold`: reduce with a starting value

`fold` is like `reduce` but takes an **initial value**, can produce a **different type**, and works on empty collections (it just returns the initial value):

```dart
void main() {
  var numbers = [1, 2, 3, 4];

  var sum = numbers.fold(0, (total, n) => total + n);
  print(sum);                          // 10

  var asText = numbers.fold('', (text, n) => '$text$n,');
  print(asText);                       // 1,2,3,4,

  var empty = <int>[];
  print(empty.fold(0, (a, b) => a + b));   // 0  (reduce would throw here)
}
```

### `expand`: turn each element into zero or more elements

`expand` maps every element to an iterable and then **flattens** the results:

```dart
void main() {
  var nested = [[1, 2], [3], [4, 5]];
  print(nested.expand((inner) => inner).toList());   // [1, 2, 3, 4, 5]

  var repeated = [1, 2, 3].expand((n) => [n, n]).toList();
  print(repeated);                                    // [1, 1, 2, 2, 3, 3]
}
```

### Chaining

Because each method returns an `Iterable`, you can chain them into a pipeline:

```dart
void main() {
  var orders = [12.5, 40.0, 8.75, 99.9, 25.0];

  var totalOfBigOrders = orders
      .where((price) => price >= 20)
      .map((price) => price * 1.12)        // add 12% tax
      .fold(0.0, (sum, price) => sum + price);

  print(totalOfBigOrders.toStringAsFixed(2));  // 184.69
}
```

### Other handy iterable methods

| Method | Purpose |
|---|---|
| `any(test)`, `every(test)` | Is at least one / are all elements passing the test? |
| `take(n)`, `skip(n)` | First `n` / all but first `n` elements |
| `takeWhile(test)`, `skipWhile(test)` | Take or skip while the test is true |
| `followedBy(other)` | Append another iterable |
| `firstWhere(test, orElse: ...)` | First matching element |
| `toList()`, `toSet()` | Convert to a concrete collection |
| `join(separator)` | Combine into one string |

---

## 15.5 Lazy Evaluation

The results of `map`, `where`, `take`, `skip`, and similar methods are **lazy**: Dart does not do the work when you call them. It does the work **only when you actually read the elements**, one at a time, and only as many as needed.

```dart
void main() {
  var squares = [1, 2, 3, 4].map((n) {
    print('computing $n');
    return n * n;
  });

  print('created');          // nothing has been computed yet
  print(squares.first);      // computes only the first element
}
```

Output:

```
created
computing 1
1
```

Calling `toList()` (or looping over everything) forces all the work:

```dart
var all = squares.toList();   // prints computing 1, 2, 3, 4
```

### Benefits

- **Efficiency:** If you only need the first few results, the rest is never computed.
- **Chaining without waste:** `where(...).map(...).take(2)` does not create intermediate lists.
- **Infinite sequences** become possible (see the generators lesson).

```dart
void main() {
  var firstTwoBigSquares = [1, 2, 3, 4, 5, 6]
      .map((n) => n * n)
      .where((sq) => sq > 10)
      .take(2)
      .toList();
  print(firstTwoBigSquares);   // [16, 25]
}
```

### A common surprise: repeated evaluation

Because a lazy iterable recomputes each time it is iterated, iterating it twice runs your function twice:

```dart
void main() {
  var doubled = [1, 2].map((n) {
    print('working on $n');
    return n * 2;
  });

  print(doubled.toList());   // working on 1, working on 2, then [2, 4]
  print(doubled.toList());   // working again, then [2, 4]
}
```

Also, a lazy iterable reflects changes to its source list at the moment you read it. If the result will be used several times, or if your function has side effects, call **`toList()` once** and keep the list.

---

## 15.6 Unmodifiable and Immutable Collections

Sometimes you want to guarantee that a collection cannot be changed, so that other code cannot accidentally alter your data. Dart gives you several ways, with important differences.

### `const` collections

Created at compile time and **deeply** unchangeable:

```dart
void main() {
  const primes = [2, 3, 5, 7];
  const config = {'debug': false, 'retries': 3};

  // primes.add(11);        // Runtime error: Unsupported operation
  // config['debug'] = true; // Runtime error
  print(primes);
}
```

All items inside a `const` collection must themselves be constants.

### `final` is not enough

`final` only stops you from reassigning the *variable*. The list itself can still change:

```dart
final names = ['Ana'];
names.add('Ben');          // allowed
// names = ['Zed'];        // ERROR: final variable
```

### `List.unmodifiable`, `Set.unmodifiable`, `Map.unmodifiable`

These create a **copy** that cannot be changed, even when the source values are computed at run time:

```dart
void main() {
  var source = [3, 1, 2];
  var locked = List.unmodifiable(source);

  source.add(4);              // changing the source does NOT affect the copy
  print(locked);              // [3, 1, 2]

  // locked.add(9);           // Runtime error: Unsupported operation
  // locked[0] = 5;           // Runtime error
}
```

The same pattern works for sets and maps:

```dart
var fixedSet = Set.unmodifiable({1, 2});
var fixedMap = Map.unmodifiable({'a': 1});
```

### Read-only views: `UnmodifiableListView`

From `dart:collection`, a view **wraps** an existing collection and blocks changes **through the view**, while the original can still change. It is useful for exposing internal data from a class without copying it:

```dart
import 'dart:collection';

void main() {
  var internal = [1, 2, 3];
  var view = UnmodifiableListView(internal);

  internal.add(4);
  print(view);          // [1, 2, 3, 4]  the view reflects the original
  // view.add(5);       // Runtime error: Unsupported operation
}
```

### Summary

| Technique | Blocks changes? | Copy or view? | Notes |
|---|---|---|---|
| `final` | No (only reassignment) | n/a | Variable is fixed, contents are not |
| `const [..]` | Yes | n/a | Compile-time, deeply immutable |
| `List.unmodifiable(x)` | Yes | **Copy** | Later changes to `x` are not seen |
| `UnmodifiableListView(x)` | Yes, through the view | **View** | Changes to `x` are visible |

Trying to modify any unmodifiable collection throws an `UnsupportedError` at run time, so choose the technique that fits how your data is created and shared.

---

[Previous](./[14]-Sets-and-Maps.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[16]-Records.md)
