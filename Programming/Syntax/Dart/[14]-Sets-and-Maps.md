[Previous](./[13]-Lists.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[15]-Collection-Operators-and-Iterables.md)

*Core Syntax*

# Lesson 14 - Collections: Sets & Maps

Lists are not the only way to group data. A **set** stores unique items with no duplicates, and a **map** stores **key-value pairs**, letting you look up a value by name instead of by position. This lesson covers both.

---

## 14.1 Sets and Uniqueness

A `Set` is an unordered-feeling collection in which **every element appears at most once**. Adding a duplicate does nothing.

### Creating sets

Use curly braces:

```dart
void main() {
  var primes = {2, 3, 5, 7};              // Set<int>
  Set<String> tags = {'dart', 'flutter'}; // explicit type
  var empty = <String>{};                 // empty set: type argument required

  print(primes);        // {2, 3, 5, 7}
  print(primes.length); // 4
}
```

> **Watch out:** `{}` with nothing inside, and no type information, creates an empty **Map**, not a set. Write `<int>{}` or `Set<int>()` for an empty set.

```dart
var a = {};          // Map<dynamic, dynamic>
var b = <int>{};     // Set<int>
Set<int> c = {};     // Set<int>, because the declared type tells Dart
```

### Adding, removing, and testing

```dart
void main() {
  var colors = {'red', 'green'};

  print(colors.add('blue'));    // true  (added)
  print(colors.add('red'));     // false (already present)
  print(colors);                // {red, green, blue}

  print(colors.contains('green'));          // true
  print(colors.containsAll(['red', 'blue'])); // true

  colors.remove('green');
  print(colors);                // {red, blue}
}
```

`contains` on a set is very fast, much faster than on a list for large collections.

### Removing duplicates from a list

A set is the easiest way to get the distinct values of a list:

```dart
void main() {
  var votes = ['a', 'b', 'a', 'c', 'b', 'a'];
  var unique = votes.toSet();
  print(unique);            // {a, b, c}
  print(unique.toList());   // [a, b, c]
}
```

### How uniqueness works

Elements are compared using `==` and `hashCode`. For numbers and strings this is by value. For your own classes, two objects count as duplicates only if you override both (covered in the object equality lesson).

The default `Set` keeps elements in **insertion order** when you iterate.

---

## 14.2 Set Operations (union, intersection, difference)

Sets support the classic mathematical operations, each returning a **new** set:

```dart
void main() {
  var a = {1, 2, 3, 4};
  var b = {3, 4, 5, 6};

  print(a.union(b));          // {1, 2, 3, 4, 5, 6}  everything in either
  print(a.intersection(b));   // {3, 4}              in both
  print(a.difference(b));     // {1, 2}              in a but not in b
  print(b.difference(a));     // {5, 6}              in b but not in a
}
```

Related checks:

```dart
void main() {
  var small = {1, 2};
  var big = {1, 2, 3};

  print(big.containsAll(small));   // true  (small is a subset of big)
  print(small.containsAll(big));   // false
}
```

A practical example: finding which students are in both clubs.

```dart
void main() {
  var chess = {'Ana', 'Ben', 'Carl'};
  var music = {'Ben', 'Dee', 'Carl'};

  print('Both: ${chess.intersection(music)}');          // Both: {Ben, Carl}
  print('Only chess: ${chess.difference(music)}');      // Only chess: {Ana}
  print('Everyone: ${chess.union(music)}');             // Everyone: {Ana, Ben, Carl, Dee}
}
```

Other useful set methods include `addAll`, `removeWhere`, `retainWhere`, and `lookup`.

---

## 14.3 Maps and Key-Value Pairs

A `Map<K, V>` associates **keys** with **values**. Keys are unique; values can repeat. You look up a value using its key.

### Creating maps

```dart
void main() {
  var ages = {
    'Ana': 21,
    'Ben': 25,
    'Carl': 19,
  };                                    // Map<String, int>

  Map<String, String> capitals = {'France': 'Paris', 'Japan': 'Tokyo'};
  var empty = <String, int>{};          // empty map

  print(ages);            // {Ana: 21, Ben: 25, Carl: 19}
  print(ages.length);     // 3
}
```

### Reading, adding, and updating

```dart
void main() {
  var ages = {'Ana': 21, 'Ben': 25};

  print(ages['Ana']);       // 21
  print(ages['Zed']);       // null: a missing key gives null, not an error

  ages['Carl'] = 19;        // add a new entry
  ages['Ana'] = 22;         // update an existing entry
  print(ages);              // {Ana: 22, Ben: 25, Carl: 19}
}
```

Because a lookup might return `null`, `ages['Ana']` has the **nullable** type `int?`. Handle it with the null-aware tools from Lesson 8:

```dart
int age = ages['Ana'] ?? 0;
int? len = ages['Zed']?.bitLength;
int sure = ages['Ana']!;    // only if you are certain the key exists
```

### Checking and removing

```dart
void main() {
  var stock = {'apple': 5, 'pear': 0};

  print(stock.containsKey('apple'));   // true
  print(stock.containsValue(0));       // true
  print(stock.isEmpty);                // false

  stock.remove('pear');
  print(stock);                        // {apple: 5}
}
```

### Handy update methods

```dart
void main() {
  var counts = {'a': 1};

  // putIfAbsent: add only if the key is missing; returns the value
  counts.putIfAbsent('b', () => 10);
  counts.putIfAbsent('a', () => 99);   // 'a' exists, so nothing changes
  print(counts);                       // {a: 1, b: 10}

  // update: change a value using the old one
  counts.update('a', (old) => old + 1);
  print(counts);                       // {a: 2, b: 10}

  // update with ifAbsent: handles a missing key
  counts.update('c', (old) => old + 1, ifAbsent: () => 1);
  print(counts);                       // {a: 2, b: 10, c: 1}

  // addAll: merge another map
  counts.addAll({'d': 4, 'a': 100});
  print(counts);                       // {a: 100, b: 10, c: 1, d: 4}
}
```

A very common pattern is counting occurrences:

```dart
void main() {
  var text = 'banana';
  var freq = <String, int>{};

  for (final ch in text.split('')) {
    freq[ch] = (freq[ch] ?? 0) + 1;
  }
  print(freq);   // {b: 1, a: 3, n: 2}
}
```

### Keys can be any type

Keys do not have to be strings. Any object works as long as it has sensible `==` and `hashCode`:

```dart
var byId = {1: 'one', 2: 'two'};              // Map<int, String>
var mixed = <Object, String>{1: 'int key', 'a': 'string key'};
```

---

## 14.4 Iterating over Maps

A map exposes its contents through three views: `keys`, `values`, and `entries` (key-value pairs).

```dart
void main() {
  var scores = {'Ana': 90, 'Ben': 75, 'Carl': 82};

  print(scores.keys);     // (Ana, Ben, Carl)
  print(scores.values);   // (90, 75, 82)
  print(scores.entries.first.key);    // Ana
  print(scores.entries.first.value);  // 90
}
```

### Looping

```dart
void main() {
  var scores = {'Ana': 90, 'Ben': 75, 'Carl': 82};

  // 1. Over entries (most common)
  for (final entry in scores.entries) {
    print('${entry.key}: ${entry.value}');
  }

  // 2. Over keys, looking up values
  for (final name in scores.keys) {
    print('$name scored ${scores[name]}');
  }

  // 3. Over values only
  var total = 0;
  for (final s in scores.values) {
    total += s;
  }
  print(total);   // 247

  // 4. forEach with key and value
  scores.forEach((name, score) => print('$name -> $score'));
}
```

### Transforming maps

```dart
void main() {
  var prices = {'apple': 2, 'pear': 3};

  // map(): build a new map from every entry
  var doubled = prices.map((key, value) => MapEntry(key, value * 2));
  print(doubled);   // {apple: 4, pear: 6}

  // Filter entries by turning them into a new map
  var expensive = Map.fromEntries(
    prices.entries.where((e) => e.value > 2),
  );
  print(expensive); // {pear: 3}

  // Build a map from a list
  var names = ['Ana', 'Ben'];
  var lengths = {for (final n in names) n: n.length};
  print(lengths);   // {Ana: 3, Ben: 3}

  // Remove entries in place
  prices.removeWhere((key, value) => value < 3);
  print(prices);    // {pear: 3}
}
```

> Do not add or remove keys while looping over a map; this throws a `ConcurrentModificationError`. Changing the value of an existing key is fine.

Note that `keys` and `values` are **iterables**, not lists. Use `.toList()` when you need a list:

```dart
var keyList = scores.keys.toList();
```

---

## 14.5 `LinkedHashMap`, `SplayTreeMap`, and Other Variants

The curly-brace literal creates a ready-made map, but Dart has several map and set **implementations** in the `dart:collection` library. They behave the same on the surface but differ in ordering and speed.

```dart
import 'dart:collection';
```

| Type | Iteration order | Notes |
|---|---|---|
| `LinkedHashMap` / `LinkedHashSet` | **Insertion order** | This is what `{...}` literals and `Map()` / `Set()` give you by default |
| `HashMap` / `HashSet` | Unspecified | Slightly less overhead; order may look random |
| `SplayTreeMap` / `SplayTreeSet` | **Sorted by key** | Keys must be comparable, or you supply a comparison function |

### Insertion order (default)

```dart
void main() {
  var m = {'z': 1, 'a': 2, 'm': 3};
  print(m.keys.toList());   // [z, a, m]
}
```

### Sorted keys with `SplayTreeMap`

```dart
import 'dart:collection';

void main() {
  var sorted = SplayTreeMap<String, int>();
  sorted['pear'] = 3;
  sorted['apple'] = 5;
  sorted['fig'] = 1;

  print(sorted);            // {apple: 5, fig: 1, pear: 3}
  print(sorted.firstKey()); // apple
  print(sorted.lastKey());  // pear
}
```

You can pass your own ordering, for example newest numbers first:

```dart
var desc = SplayTreeMap<int, String>((a, b) => b.compareTo(a));
desc[1] = 'one';
desc[3] = 'three';
desc[2] = 'two';
print(desc);   // {3: three, 2: two, 1: one}
```

`SplayTreeSet` works the same way for sets:

```dart
var ordered = SplayTreeSet<int>()..addAll([5, 1, 4]);
print(ordered);   // {1, 4, 5}
```

### Other useful collections

| Type | Purpose |
|---|---|
| `Queue` / `ListQueue` | Fast adding and removing at both ends (queues, stacks) |
| `UnmodifiableMapView` | A read-only view of a map |
| `UnmodifiableListView` | A read-only view of a list |

For everyday code, the default literals are the right choice. Reach for `SplayTreeMap` when you need keys kept sorted automatically, and `HashMap` only if profiling shows it matters.

---

[Previous](./[13]-Lists.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[15]-Collection-Operators-and-Iterables.md)
