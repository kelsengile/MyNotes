[Previous](./[12]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[14]-Sets-and-Maps.md)

*Core Syntax*

# Lesson 13 - Collections: Lists

A **collection** stores many values under one name. The **list** is the most common collection in Dart: an ordered group of items, where each item has a numbered position called an **index**. (Dart calls its list type `List`; other languages call it an array.)

---

## 13.1 Creating Lists

### List literals

Write items between square brackets, separated by commas. Dart infers the element type:

```dart
void main() {
  var numbers = [10, 20, 30];            // List<int>
  var names = ['Ana', 'Ben', 'Carl'];    // List<String>
  var mixed = [1, 'two', 3.0];           // List<Object>
  List<double> prices = [1.5, 2.25];     // explicit type
  var empty = <String>[];                // empty list: specify the type argument

  print(numbers);       // [10, 20, 30]
  print(empty.length);  // 0
}
```

A trailing comma after the last item is allowed and helps the formatter keep long lists readable:

```dart
var colors = [
  'red',
  'green',
  'blue',
];
```

### Constructors

```dart
void main() {
  // A list of 3 zeros
  var zeros = List<int>.filled(3, 0);
  print(zeros);   // [0, 0, 0]

  // Generate each element from its index
  var squares = List.generate(5, (i) => i * i);
  print(squares); // [0, 1, 4, 9, 16]

  // Copy another collection
  var copy = List.of([1, 2, 3]);
  print(copy);    // [1, 2, 3]

  // Turn any iterable into a list
  var chars = 'abc'.split('').toList();
  var evens = [1, 2, 3, 4].where((n) => n.isEven).toList();
  print(evens);   // [2, 4]
}
```

### Constant lists

A list created with `const` is **unmodifiable** and shared in memory:

```dart
const days = ['Mon', 'Tue', 'Wed'];
// days.add('Thu');   // Runtime error: unsupported operation
```

---

## 13.2 Accessing and Modifying Elements

### Reading

Indexes start at **0**. Use `[]` with an index:

```dart
void main() {
  var fruits = ['apple', 'banana', 'cherry'];

  print(fruits[0]);        // apple
  print(fruits[2]);        // cherry
  print(fruits.first);     // apple
  print(fruits.last);      // cherry
  print(fruits.length);    // 3
  print(fruits.isEmpty);   // false
  print(fruits.isNotEmpty);// true

  // print(fruits[3]);     // RangeError: index out of range
}
```

The last valid index is always `length - 1`. Reading outside the range throws a `RangeError`.

Calling `first`, `last`, or `single` on an empty list throws a `StateError`. Use `firstOrNull` and `lastOrNull` when the list may be empty:

```dart
var none = <int>[];
print(none.firstOrNull);   // null
```

### Changing and adding

```dart
void main() {
  var list = ['a', 'b', 'c'];

  list[1] = 'B';            // replace an element
  list.add('d');            // add to the end
  list.addAll(['e', 'f']);  // add several to the end
  list.insert(0, 'start');  // insert at an index
  list.insertAll(1, ['x', 'y']);

  print(list);
  // [start, x, y, a, B, c, d, e, f]
}
```

### Removing

```dart
void main() {
  var list = [10, 20, 30, 20, 40];

  list.remove(20);          // removes the first 20 -> [10, 30, 20, 40]
  list.removeAt(0);         // removes index 0     -> [30, 20, 40]
  var last = list.removeLast(); // returns 40      -> [30, 20]
  list.removeWhere((n) => n > 25); //              -> [20]
  print(list);              // [20]

  list.clear();             // remove everything
  print(list);              // []
}
```

`remove` returns a `bool` saying whether it found the item. `removeAt` and `removeLast` return the removed element.

### Lists are objects (reference semantics)

Assigning a list to another variable does **not** copy it; both names refer to the same list:

```dart
void main() {
  var a = [1, 2, 3];
  var b = a;            // same list
  b.add(4);
  print(a);             // [1, 2, 3, 4]

  var c = [...a];       // a real copy (spread, see Lesson 15)
  c.add(5);
  print(a);             // [1, 2, 3, 4]  (unchanged)
  print(c);             // [1, 2, 3, 4, 5]
}
```

---

## 13.3 Fixed-Length vs Growable Lists

A **growable** list can change size (add, remove, insert). A **fixed-length** list cannot, although its elements can still be replaced.

| How created | Length |
|---|---|
| `[1, 2, 3]` literal | Growable |
| `List.generate(n, f)` | Growable by default |
| `List.of(x)`, `x.toList()` | Growable by default |
| `List<int>.filled(3, 0)` | **Fixed-length** by default |
| `List<int>.filled(3, 0, growable: true)` | Growable |
| `const [1, 2, 3]` | Unmodifiable |
| `List.unmodifiable(x)` | Unmodifiable |

```dart
void main() {
  var fixed = List<int>.filled(3, 0);
  fixed[0] = 99;                 // OK: replacing an element
  print(fixed);                  // [99, 0, 0]
  // fixed.add(4);               // Runtime error: cannot add to a fixed-length list

  var growable = List<int>.filled(3, 0, growable: true);
  growable.add(4);               // OK
  print(growable);               // [0, 0, 0, 4]

  var frozen = List.unmodifiable([1, 2, 3]);
  // frozen[0] = 9;              // Runtime error: cannot modify an unmodifiable list
  print(frozen);
}
```

You can also change the length of a growable list directly, though this is rarely needed:

```dart
var items = [1, 2, 3, 4];
items.length = 2;
print(items);  // [1, 2]
```

Fixed-length lists use a little less memory and make your intent clear. Growable lists are the everyday default.

---

## 13.4 Common List Methods

```dart
void main() {
  var nums = [5, 3, 8, 3, 1];

  // Searching
  print(nums.contains(8));              // true
  print(nums.indexOf(3));               // 1
  print(nums.lastIndexOf(3));           // 3
  print(nums.indexOf(99));              // -1
  print(nums.indexWhere((n) => n > 5)); // 2

  // Checking conditions
  print(nums.any((n) => n > 7));        // true: at least one
  print(nums.every((n) => n > 0));      // true: all of them

  // Finding the first match
  print(nums.firstWhere((n) => n > 4)); // 5
  print(nums.firstWhere((n) => n > 100, orElse: () => -1)); // -1

  // Other
  print(nums.reversed.toList());        // [1, 3, 8, 3, 5]
  print(nums.join('-'));                // 5-3-8-3-1
  print(nums.toSet());                  // {5, 3, 8, 1}
}
```

`firstWhere` throws a `StateError` if nothing matches and no `orElse` is given, so supply `orElse` or use `firstWhereOrNull` (from `package:collection`) when a match is not guaranteed.

Note that `reversed` returns a lazy **Iterable**, not a list; call `toList()` to get a list.

### Printing and converting

```dart
var list = [1, 2, 3];
print(list.toString());     // [1, 2, 3]
print('List: $list');       // List: [1, 2, 3]
```

### Quick reference

| Method | Purpose |
|---|---|
| `add`, `addAll`, `insert`, `insertAll` | Add elements |
| `remove`, `removeAt`, `removeLast`, `removeWhere`, `clear` | Remove elements |
| `contains`, `indexOf`, `indexWhere` | Search |
| `any`, `every` | Test conditions |
| `firstWhere`, `where` | Find or filter |
| `sublist`, `take`, `skip` | Take parts |
| `sort`, `shuffle` | Reorder (in place) |
| `join` | Combine into a string |

---

## 13.5 Sorting, Searching, and Slicing

### Sorting

`sort()` reorders the list **in place** and returns nothing. For numbers and strings, the default order works:

```dart
void main() {
  var nums = [5, 3, 8, 1];
  nums.sort();
  print(nums);          // [1, 3, 5, 8]

  var words = ['pear', 'apple', 'fig'];
  words.sort();
  print(words);         // [apple, fig, pear]
}
```

To sort another way, pass a **comparison function** that takes two items `a` and `b` and returns a negative number if `a` should come first, a positive number if `b` should come first, or zero if they are equal:

```dart
void main() {
  var nums = [5, 3, 8, 1];
  nums.sort((a, b) => b.compareTo(a));       // descending
  print(nums);                                // [8, 5, 3, 1]

  var words = ['pear', 'apple', 'fig'];
  words.sort((a, b) => a.length.compareTo(b.length));  // by length
  print(words);                               // [fig, pear, apple]
}
```

Because `sort()` changes the original, copy first if you need to keep the original order:

```dart
var original = [3, 1, 2];
var sorted = [...original]..sort();
print(original);   // [3, 1, 2]
print(sorted);     // [1, 2, 3]
```

### Shuffling

```dart
var cards = [1, 2, 3, 4, 5];
cards.shuffle();   // random order each run
```

### Slicing

Slicing means taking part of a list. The end index is **exclusive**:

```dart
void main() {
  var list = [10, 20, 30, 40, 50];

  print(list.sublist(1, 4));    // [20, 30, 40]
  print(list.sublist(2));       // [30, 40, 50]
  print(list.take(2).toList()); // [10, 20]        first 2
  print(list.skip(3).toList()); // [40, 50]        all but first 3
  print(list.getRange(1, 3).toList()); // [20, 30]
}
```

`sublist` returns a new list. `take`, `skip`, and `getRange` return lazy iterables, so call `toList()` when you need a list.

### Searching in sorted data

For a list that is already sorted, you can find items quickly with a binary search, available through `package:collection` (`lowerBound`, `binarySearch`). For ordinary lists, `contains`, `indexOf`, and `indexWhere` are all you need.

---

## 13.6 Multidimensional Lists

Dart has no special "2D array" type. Instead, you create a **list of lists**:

```dart
void main() {
  var grid = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
  ];

  print(grid[1][2]);       // 6  (row 1, column 2)
  grid[0][0] = 100;
  print(grid[0]);          // [100, 2, 3]

  for (final row in grid) {
    print(row.join(' '));
  }
}
```

Output of the loop:

```
100 2 3
4 5 6
7 8 9
```

### Creating an empty grid

Use `List.generate` so that each row is a **separate** list:

```dart
void main() {
  var rows = 3, cols = 4;
  var board = List.generate(rows, (_) => List.filled(cols, 0));

  board[1][2] = 5;
  for (final row in board) {
    print(row);
  }
}
```

Output:

```
[0, 0, 0, 0]
[0, 0, 5, 0]
[0, 0, 0, 0]
```

### A common mistake

Do **not** write `List.filled(rows, List.filled(cols, 0))`. That puts the *same* inner list in every row, so changing one row changes them all:

```dart
void main() {
  var wrong = List.filled(3, List.filled(3, 0));
  wrong[0][0] = 1;
  print(wrong);   // [[1, 0, 0], [1, 0, 0], [1, 0, 0]]  (all rows changed!)
}
```

### Rows with different lengths

Because each row is its own list, rows may have different lengths (a "jagged" list):

```dart
var triangle = [
  [1],
  [1, 1],
  [1, 2, 1],
];
print(triangle[2].length);   // 3
```

---

[Previous](./[12]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[14]-Sets-and-Maps.md)
