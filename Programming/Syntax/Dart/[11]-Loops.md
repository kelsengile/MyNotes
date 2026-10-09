[Previous](./[10]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[12]-Functions.md)

*Core Syntax*

# Lesson 11 - Loops: for, while, do-while, for-in

A **loop** repeats a block of code. Instead of writing the same lines ten times, you write them once and tell Dart how long to keep going. This lesson covers every loop style, ways to exit or skip an iteration, and functional alternatives for working with collections.

---

## 11.1 The `for` Loop

The classic `for` loop has three parts inside the parentheses, separated by semicolons:

```
for (initialization; condition; update) {
  body
}
```

1. **Initialization** runs once before the loop begins.
2. **Condition** is checked before each pass; the loop continues while it is `true`.
3. **Update** runs after each pass.

```dart
void main() {
  for (var i = 0; i < 5; i++) {
    print('Count: $i');
  }
}
```

Output:

```
Count: 0
Count: 1
Count: 2
Count: 3
Count: 4
```

Variations:

```dart
// Count down
for (var i = 10; i > 0; i -= 3) {
  print(i);          // 10, 7, 4, 1
}

// Sum 1 to 100
var sum = 0;
for (var n = 1; n <= 100; n++) {
  sum += n;
}
print(sum);          // 5050
```

### Nested loops

A loop inside another loop runs completely for each pass of the outer loop:

```dart
void main() {
  for (var row = 1; row <= 3; row++) {
    var line = '';
    for (var col = 1; col <= 3; col++) {
      line += '${row * col}\t';
    }
    print(line);
  }
}
```

Output (a small multiplication table):

```
1	2	3	
2	4	6	
3	6	9	
```

### Closures capture a fresh variable per iteration

In Dart, each pass through a `for` loop gets its **own copy** of the loop variable. This matters when you store functions inside the loop (a feature covered in Lesson 12):

```dart
void main() {
  var callbacks = <void Function()>[];
  for (var i = 0; i < 3; i++) {
    callbacks.add(() => print(i));
  }
  for (var cb in callbacks) {
    cb();   // 0, 1, 2  (not 3, 3, 3 as in some other languages)
  }
}
```

---

## 11.2 The `for-in` Loop

When you just want to visit each element of a collection, use `for-in`. It works with any `Iterable` (lists, sets, the keys of a map, and so on) and avoids index mistakes:

```dart
void main() {
  var fruits = ['apple', 'banana', 'cherry'];

  for (var fruit in fruits) {
    print('I like $fruit');
  }
}
```

Use `final` when you do not intend to change the loop variable (this is a good habit):

```dart
for (final fruit in fruits) {
  print(fruit.toUpperCase());
}
```

### Getting the index too

`for-in` does not give you the position. When you need both the index and the value, use the `indexed` property (Dart 3), which yields pairs:

```dart
void main() {
  var fruits = ['apple', 'banana', 'cherry'];

  for (final (index, fruit) in fruits.indexed) {
    print('$index: $fruit');
  }
}
```

Output:

```
0: apple
1: banana
2: cherry
```

(The `(index, fruit)` syntax unpacks a pair; it is explained in the records and patterns lessons.) A classic indexed `for` loop works just as well:

```dart
for (var i = 0; i < fruits.length; i++) {
  print('$i: ${fruits[i]}');
}
```

### Looping over other collections

```dart
void main() {
  var unique = {3, 1, 2};
  for (final n in unique) {
    print(n);                  // 3, 1, 2 (insertion order)
  }

  var ages = {'ana': 20, 'ben': 25};
  for (final name in ages.keys) {
    print('$name -> ${ages[name]}');
  }

  for (final c in 'dart'.split('')) {
    print(c);                  // d, a, r, t
  }
}
```

> **Warning:** Do not add or remove items from a list while looping over it with `for-in`; Dart throws a `ConcurrentModificationError`. Build a new list or loop over a copy (`for (final x in list.toList())`) instead.

---

## 11.3 `while` and `do-while`

### `while`

A `while` loop checks its condition **before** each pass, so the body may run zero times:

```dart
void main() {
  var n = 1;
  while (n < 100) {
    n *= 2;
  }
  print(n);   // 128
}
```

Use `while` when you do not know in advance how many passes you need, for example "keep reading until the user types quit".

### `do-while`

A `do-while` loop checks its condition **after** each pass, so the body always runs **at least once**:

```dart
void main() {
  var attempts = 0;

  do {
    attempts++;
    print('Attempt $attempts');
  } while (attempts < 3);
}
```

Output:

```
Attempt 1
Attempt 2
Attempt 3
```

Compare the two when the condition is false from the start:

```dart
var x = 10;
while (x < 5) { print('while'); }       // prints nothing
do { print('do-while'); } while (x < 5); // prints "do-while" once
```

### Infinite loops

A loop whose condition never becomes false runs forever. Sometimes that is intentional, with a `break` inside to leave it:

```dart
var count = 0;
while (true) {
  count++;
  if (count == 5) break;
}
print(count);  // 5
```

Make sure the variable in your condition really does change inside the loop.

---

## 11.4 `break`, `continue`, and Labels

### `break`

`break` stops the loop immediately and continues with the code after it:

```dart
void main() {
  for (var i = 1; i <= 10; i++) {
    if (i == 4) break;
    print(i);          // 1, 2, 3
  }
}
```

### `continue`

`continue` skips the rest of the current pass and jumps to the next one:

```dart
void main() {
  for (var i = 1; i <= 6; i++) {
    if (i.isOdd) continue;
    print(i);          // 2, 4, 6
  }
}
```

### Labels

When loops are nested, `break` and `continue` affect only the **innermost** loop. A **label** (a name followed by a colon) lets you target an outer loop:

```dart
void main() {
  outer:
  for (var i = 1; i <= 3; i++) {
    for (var j = 1; j <= 3; j++) {
      if (j == 2) continue outer;   // skip to next i
      if (i == 3) break outer;      // leave both loops
      print('i=$i j=$j');
    }
  }
}
```

Output:

```
i=1 j=1
i=2 j=1
```

Labels are rarely needed. If you find yourself using them often, consider moving the inner loop into its own function and using `return`.

---

## 11.5 `forEach` and Functional Iteration

Collections have a `forEach` method that takes a function and calls it for every element:

```dart
void main() {
  var names = ['Ana', 'Ben', 'Carl'];

  names.forEach((name) {
    print('Hello, $name');
  });

  // Arrow function form
  names.forEach((name) => print(name.length));

  // Passing an existing function (a "tear-off", see Lesson 12)
  names.forEach(print);
}
```

For maps, the function receives the key and the value:

```dart
var ages = {'ana': 20, 'ben': 25};
ages.forEach((name, age) => print('$name is $age'));
```

### `forEach` vs `for-in`

| | `for-in` | `forEach` |
|---|---|---|
| Can use `break` / `continue` | Yes | No |
| `return` inside the body | Exits the whole function | Only ends the current call (like `continue`) |
| Works with `await` inside | Yes | Not reliably |
| Style guide preference | **Preferred** for plain looping | Fine with a tear-off such as `forEach(print)` |

Because of these differences, the Dart style guide recommends `for-in` for general loops.

### Transforming with functional methods

Often you do not want to *do* something with each element, but to *produce a new collection*. The functional methods are the right tool (they are covered in depth in Lesson 15):

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5, 6];

  var squares = numbers.map((n) => n * n).toList();
  var evens = numbers.where((n) => n.isEven).toList();
  var total = numbers.fold(0, (sum, n) => sum + n);

  print(squares);  // [1, 4, 9, 16, 25, 36]
  print(evens);    // [2, 4, 6]
  print(total);    // 21
}
```

To create a list by "looping" a fixed number of times, use `List.generate`:

```dart
var tens = List.generate(5, (i) => (i + 1) * 10);
print(tens);   // [10, 20, 30, 40, 50]
```

---

[Previous](./[10]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[12]-Functions.md)
