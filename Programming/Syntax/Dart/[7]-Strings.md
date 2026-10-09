[Previous](./[6]-Numbers-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[8]-Null-Safety.md)

*Core Syntax*

# Lesson 7 - Strings & Text

Text is everywhere in programs: names, messages, file contents, web data. Dart's `String` type is powerful and easy to use. This lesson covers how to create strings, combine them, transform them, and handle tricky characters such as emoji.

---

## 7.1 Creating Strings

A Dart `String` is a sequence of **UTF-16 code units**. You can write string literals with either single or double quotes. They are equivalent, and the Dart style guide prefers single quotes unless the text itself contains a single quote.

```dart
void main() {
  var a = 'Hello';
  var b = "World";
  var c = "It's easy";        // double quotes avoid escaping the apostrophe
  var d = 'It\'s also easy';  // or escape with a backslash
  print('$a $b');
}
```

### Escape sequences

| Sequence | Meaning |
|---|---|
| `\n` | New line |
| `\t` | Tab |
| `\\` | A backslash |
| `\'` and `\"` | A quote character |
| `\$` | A literal dollar sign (stops interpolation) |
| `\u0041` | Unicode character by 4-digit hex code (`A`) |
| `\u{1F600}` | Unicode character by longer hex code (😀) |

```dart
print('Line one\nLine two');
print('Name:\tAna');
print('Price: \$5');   // Price: $5
```

### Joining strings

```dart
void main() {
  var full = 'Hello' + ', ' + 'Dart';  // using +
  print(full);

  var adjacent = 'Hello, '  'Dart';    // neighbouring literals join automatically
  print(adjacent);

  print('ha' * 3);                      // hahaha (repeat with *)
}
```

### Strings are immutable

Once created, a string cannot be changed. Methods like `toUpperCase()` return a **new** string:

```dart
var s = 'dart';
var upper = s.toUpperCase();
print(s);      // dart (unchanged)
print(upper);  // DART
```

### Comparing strings

Use `==` to compare the **contents** of two strings:

```dart
print('abc' == 'abc');                // true
print('apple'.compareTo('banana'));   // negative number: 'apple' comes first
```

---

## 7.2 String Interpolation

Interpolation inserts values directly into a string.

- `$name` inserts a simple variable.
- `${expression}` inserts the result of any expression.

```dart
void main() {
  var name = 'Ana';
  var items = [1, 2, 3];
  var price = 4.5;

  print('Hello, $name!');                       // Hello, Ana!
  print('You have ${items.length} items.');     // You have 3 items.
  print('Total: ${price * 2}');                 // Total: 9.0
  print('Uppercase: ${name.toUpperCase()}');    // Uppercase: ANA
}
```

Rules to remember:

- Use `$name` without braces only for a **plain identifier**. For anything else (property access, method calls, math), use `${...}`.
- `'$name.length'` prints the name followed by the text ".length"; it does **not** call `length`. Write `'${name.length}'` instead.
- Interpolation calls the object's `toString()` method automatically.
- To print a literal `$`, escape it: `'\$5'`.

```dart
var word = 'dart';
print('$word.length');     // dart.length   (probably not what you wanted)
print('${word.length}');   // 4
```

---

## 7.3 Multiline and Raw Strings

### Multiline strings

Use triple quotes to write text that spans several lines. Line breaks and indentation inside the quotes are kept exactly as typed:

```dart
void main() {
  var poem = '''
Roses are red,
Violets are blue,
Dart is fun,
And so are you.
''';
  print(poem);
}
```

Triple double-quotes (`"""`) work the same way. Interpolation also works inside multiline strings.

### Raw strings

A **raw string** is written with the prefix `r`. Backslashes and `$` are treated as plain characters, with no escapes and no interpolation:

```dart
void main() {
  print(r'C:\Users\Ana\notes.txt');   // C:\Users\Ana\notes.txt
  print(r'Cost: $5 and \n stays');    // Cost: $5 and \n stays
}
```

Without the `r`, the `\U` in the first example would be an invalid escape sequence and `\n` in the second would become a line break. Raw strings are especially handy for file paths and regular expressions.

---

## 7.4 Common String Methods

```dart
void main() {
  var s = '  Hello, Dart!  ';

  // Properties
  print(s.length);          // 16
  print(s.isEmpty);         // false
  print(s.isNotEmpty);      // true

  // Trimming and case
  print(s.trim());          // "Hello, Dart!"
  print(s.trimLeft());      // "Hello, Dart!  "
  print(s.trimRight());     // "  Hello, Dart!"
  print(s.toUpperCase());   // "  HELLO, DART!  "
  print(s.toLowerCase());   // "  hello, dart!  "
}
```

### Searching

```dart
void main() {
  var t = 'Dart is fun, Dart is fast';

  print(t.contains('fun'));        // true
  print(t.startsWith('Dart'));     // true
  print(t.endsWith('fast'));       // true
  print(t.indexOf('Dart'));        // 0
  print(t.lastIndexOf('Dart'));    // 13
  print(t.indexOf('Python'));      // -1 (not found)
}
```

### Extracting and replacing

```dart
void main() {
  var t = 'Hello, Dart!';

  print(t.substring(7));            // Dart!
  print(t.substring(0, 5));         // Hello   (end index is exclusive)
  print(t[0]);                      // H       (a String of length 1)
  print(t.replaceAll('l', 'L'));    // HeLLo, Dart!
  print(t.replaceFirst('l', 'L'));  // HeLlo, Dart!
}
```

### Splitting and joining

```dart
void main() {
  var csv = 'red,green,blue';
  var parts = csv.split(',');       // [red, green, blue]
  print(parts.length);              // 3

  print(parts.join(' | '));         // red | green | blue
  print('a b  c'.split(' '));       // [a, b, , c]  (note the empty item)
}
```

### Padding

```dart
print('7'.padLeft(3, '0'));    // 007
print('Hi'.padRight(5, '.'));  // Hi...
```

### Character codes

```dart
print('A'.codeUnitAt(0));          // 65
print(String.fromCharCode(97));    // a
print(String.fromCharCodes([72, 105])); // Hi
```

Accessing an index outside the string, such as `'abc'[5]`, throws a `RangeError`.

---

## 7.5 `StringBuffer`

Because strings are immutable, building a long string with `+` inside a loop creates many temporary strings. A `StringBuffer` collects pieces efficiently and creates the final string once:

```dart
void main() {
  var buffer = StringBuffer();

  for (var i = 1; i <= 5; i++) {
    buffer.write(i);
    if (i < 5) buffer.write(', ');
  }

  buffer.writeln();               // adds a newline
  buffer.writeAll(['a', 'b', 'c'], '-');

  print(buffer.toString());
  // 1, 2, 3, 4, 5
  // a-b-c
  print(buffer.length);           // number of code units so far
}
```

Handy members:

| Member | Purpose |
|---|---|
| `write(obj)` | Append the text of any object |
| `writeln([obj])` | Append and add a newline |
| `writeAll(items, [separator])` | Append many items with an optional separator |
| `writeCharCode(code)` | Append one character from a code |
| `clear()` | Empty the buffer |
| `toString()` | Produce the final `String` |

Use a `StringBuffer` when you build text in loops or from many pieces; use plain interpolation for small, one-off strings.

---

## 7.6 Runes and Grapheme Clusters

Text is more complicated than it looks. A single thing a person sees as "one character" may be stored as several numbers. There are three levels:

| Level | What it is | Access in Dart |
|---|---|---|
| **UTF-16 code unit** | Smallest storage unit | `string.length`, `string[i]`, `codeUnits` |
| **Rune** (Unicode code point) | One Unicode character number | `string.runes` |
| **Grapheme cluster** | What a human perceives as one character | `package:characters` |

Many common characters are a single code unit, but emoji and some scripts need more:

```dart
void main() {
  var smile = '🙂';
  print(smile.length);              // 2  (two UTF-16 code units)
  print(smile.runes.length);        // 1  (one Unicode code point)
  print(smile.runes.first);         // 128578
  print(smile.codeUnits);           // [55357, 56898]

  var flag = '🇵🇭';
  print(flag.length);               // 4  (two code points, each 2 code units)
  print(flag.runes.length);         // 2
}
```

So `length` counts code units, which can be larger than the number of visible characters. Indexing and substrings can even split an emoji in half and produce garbage.

### Grapheme clusters with `package:characters`

To work with *visible* characters correctly, add the `characters` package to your project:

```bash
dart pub add characters
```

Then use the `.characters` getter:

```dart
import 'package:characters/characters.dart';

void main() {
  var flag = '🇵🇭';
  var family = '👨‍👩‍👧';

  print(flag.length);               // 4
  print(flag.characters.length);    // 1
  print(family.length);             // 8
  print(family.runes.length);       // 5
  print(family.characters.length);  // 1

  var text = 'Hi 🙂!';
  print(text.characters.length);    // 5
  print(text.characters.take(4));   // Hi 🙂
}
```

Creating a string from runes:

```dart
print(String.fromCharCode(0x1F642));        // 🙂
print(String.fromCharCodes([0x48, 0x69]));  // Hi
print('\u{1F642}');                         // 🙂
```

**When does this matter?** If you only work with English letters and digits, `length` and indexing are fine. If your text may contain emoji, accented letters written as combined characters, or non-Latin scripts (for example user-entered names or chat messages), use `characters` for counting, truncating, or reversing text.

---

[Previous](./[6]-Numbers-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[8]-Null-Safety.md)
