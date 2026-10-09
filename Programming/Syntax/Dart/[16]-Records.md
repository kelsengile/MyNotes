[Previous](./[15]-Collection-Operators-and-Iterables.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[17]-Patterns-and-Destructuring.md)

*Core Syntax*

# Lesson 16 - Records

Sometimes you need to bundle a few values together, such as a name and an age, or a minimum and a maximum, without creating a whole class. **Records** (added in Dart 3) are lightweight, immutable groups of values that you can create on the spot, return from functions, and compare by their contents.

> Records require Dart 3.0 or later (`sdk: ^3.0.0` in `pubspec.yaml`).

---

## 16.1 What Is a Record?

A **record** is an anonymous, immutable, fixed-size collection of values. You write one by placing values inside parentheses:

```dart
void main() {
  var person = ('Ana', 21);
  print(person);   // (Ana, 21)
}
```

Key properties:

- **Anonymous:** you do not declare a type or name for it first.
- **Immutable:** once created, its fields cannot be changed.
- **Fixed size:** a record always has the same number of fields.
- **Mixed types:** each field can have a different type (unlike a `List`, where elements usually share one type).
- **Value-based equality:** two records with the same contents are equal (see 16.4).

Compare the options for grouping values:

| Need | Good choice |
|---|---|
| Many values of the same kind, changing size | `List` |
| A few values of different types, no behavior | **Record** |
| Values plus methods, validation, or a name | Class |

You can annotate the type of a record variable by listing the field types in parentheses:

```dart
(String, int) person = ('Ana', 21);
```

A record can hold any values, including lists, other records, or `null`:

```dart
var data = ('scores', [90, 85], (1, 2), null);
```

Two special cases to know:

```dart
var single = (42,);   // a record with ONE field needs a trailing comma
var empty = ();       // the empty record
```

Without the comma, `(42)` is just the number 42 wrapped in parentheses.

---

## 16.2 Positional and Named Fields

Records have two kinds of fields, and you can use both in one record.

### Positional fields

These are accessed with the getters `$1`, `$2`, `$3`, and so on, **starting at 1** (not 0):

```dart
void main() {
  var person = ('Ana', 21, true);

  print(person.$1);   // Ana
  print(person.$2);   // 21
  print(person.$3);   // true
}
```

### Named fields

Give a field a name with `name: value`. The name becomes a getter:

```dart
void main() {
  var point = (x: 3, y: 4);

  print(point.x);   // 3
  print(point.y);   // 4
  print(point);     // (x: 3, y: 4)
}
```

### Mixing both

```dart
void main() {
  var item = ('pen', 2.5, quantity: 10);

  print(item.$1);         // pen
  print(item.$2);         // 2.5
  print(item.quantity);   // 10
  print(item);            // (pen, 2.5, quantity: 10)
}
```

Positional fields are numbered only among the positional ones; named fields do not use up a number.

### Type annotations

Named fields are written inside **curly braces** within the parentheses:

```dart
(int, int) a = (1, 2);                         // two positional
({int x, int y}) b = (x: 1, y: 2);             // two named
(String, {int quantity}) c = ('pen', quantity: 10);  // one of each
```

### Rules and limits

- Records are **immutable**: `person.$1 = 'Ben';` is an error. To "change" a record, create a new one.
- Field names cannot start with an underscore (so records have no private fields), and cannot clash with members every object has, such as `hashCode`, `toString`, or `runtimeType`.
- Named fields can be written in any order when creating a record; the order does not affect its type or equality.
- Records are not lists: you cannot loop over their fields or access them by a variable index.

---

## 16.3 Returning Multiple Values from Functions

The most popular use of records is to return **more than one value** from a function without writing a class.

```dart
(int, int) minMax(List<int> values) {
  var lowest = values.first;
  var highest = values.first;
  for (final v in values) {
    if (v < lowest) lowest = v;
    if (v > highest) highest = v;
  }
  return (lowest, highest);
}

void main() {
  var result = minMax([4, 9, 1, 7]);
  print(result.$1);   // 1
  print(result.$2);   // 9
}
```

### Unpacking with destructuring

Rather than using `$1` and `$2`, you can unpack the record directly into variables (patterns are covered fully in Lesson 17):

```dart
void main() {
  var (lowest, highest) = minMax([4, 9, 1, 7]);
  print('Range: $lowest to $highest');   // Range: 1 to 9
}
```

### Named fields make results self-describing

For results where the order might be confusing, use named fields:

```dart
({int quotient, int remainder}) divide(int a, int b) {
  return (quotient: a ~/ b, remainder: a % b);
}

void main() {
  var d = divide(17, 5);
  print(d.quotient);    // 3
  print(d.remainder);   // 2

  // Destructure by name: a colon before the variable reuses the field name
  var (:quotient, :remainder) = divide(20, 6);
  print('$quotient r $remainder');   // 3 r 2
}
```

### Other handy uses

```dart
// A list of records
var people = [('Ana', 21), ('Ben', 19), ('Carl', 25)];
people.sort((a, b) => a.$2.compareTo(b.$2));   // sort by age
print(people);   // [(Ben, 19), (Ana, 21), (Carl, 25)]

// Loop with destructuring
for (final (name, age) in people) {
  print('$name is $age');
}

// A map of coordinates
var visited = <(int, int), String>{
  (0, 0): 'start',
  (1, 2): 'treasure',
};
print(visited[(1, 2)]);   // treasure

// Swapping two variables
var a = 1, b = 2;
(a, b) = (b, a);
print('$a $b');   // 2 1
```

Records also combine nicely with `Future`, as in `Future<(int, String)>`, to return several results from asynchronous work.

**When to switch to a class:** if the group of values has behavior (methods), needs validation, appears in many places, or deserves a meaningful name, a class is clearer. Records are best for quick, local bundling.

---

## 16.4 Record Types and Equality

### Structural types

A record's type is determined by its **shape**: the number and types of positional fields and the names and types of named fields.

```dart
(int, String) a = (1, 'x');
(int, String) b = (2, 'y');     // same type as a

({int x, int y}) p = (x: 1, y: 2);
({int y, int x}) q = (y: 2, x: 1);   // same type as p (order of names is irrelevant)
```

A record type is a subtype of another if each field's type is a subtype:

```dart
(int, int) exact = (1, 2);
(num, num) wider = exact;      // OK: int is a subtype of num
Object anything = exact;       // OK: every record is an Object
```

You can test types at run time with `is`, including inside `switch`:

```dart
void main() {
  Object value = (1, 'one');

  print(value is (int, String));   // true
  print(value is (int, int));      // false

  if (value case (int n, String s)) {
    print('$n is called $s');       // 1 is called one
  }
}
```

### Equality

Records are compared **by value**. Two records are equal if they have the same shape and all corresponding fields are equal (using `==`):

```dart
void main() {
  print((1, 2) == (1, 2));                   // true
  print((1, 2) == (2, 1));                   // false
  print((x: 1, y: 2) == (y: 2, x: 1));       // true (named order is irrelevant)
  print((1, 2) == (x: 1, y: 2));             // false (different shape)
}
```

Note that equality is **shallow**: fields are compared with their own `==`, so two different list objects are not equal even if their contents match:

```dart
print((1, [2]) == (1, [2]));   // false: the two lists are different objects
```

Records also have a matching `hashCode`, so they work well as `Map` keys and `Set` elements:

```dart
var seen = <(int, int)>{};
seen.add((1, 2));
seen.add((1, 2));          // duplicate, ignored
print(seen.length);        // 1
```

Records with only constant fields can be `const` and are then canonicalized, like other constants:

```dart
const origin = (0, 0);
```

---

[Previous](./[15]-Collection-Operators-and-Iterables.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[17]-Patterns-and-Destructuring.md)

