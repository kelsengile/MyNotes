[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[5]-Variables-and-Type-Inference.md)

*Getting Started*

# Lesson 4 - Comments & Documentation

Comments explain *why* code does something. Documentation comments go a step further: tools turn them into browsable web pages and show them in your editor when someone hovers over your code. This lesson covers all three kinds of comments in Dart.

---

## 4.1 Single-Line and Multi-Line Comments

**Single-line comments** start with `//` and run to the end of the line:

```dart
void main() {
  // This whole line is a comment.
  var price = 100; // This comment follows code.
  print(price);
}
```

**Multi-line comments** start with `/*` and end with `*/`. They can span any number of lines, and Dart allows them to be nested:

```dart
void main() {
  /*
    This is a longer note
    that spans several lines.
  */
  print('Hello');

  /* Temporarily disable code:
  print('Skipped');
  /* a nested comment is fine */
  */
}
```

Guidelines for good comments:

- Explain **why**, not **what**. `// Retry because the server is sometimes slow` is helpful; `// add one to i` is not.
- Prefer clear names over comments that explain confusing names.
- Delete commented-out code instead of leaving it behind; version control remembers it.
- In practice, most Dart code uses `//` even for several lines in a row, and saves `/* */` for temporarily commenting out blocks.

---

## 4.2 Documentation Comments (`///`)

A **documentation comment** starts with three slashes `///` and is placed directly above the thing it describes: a function, class, variable, or library.

```dart
/// Returns the area of a circle with the given [radius].
///
/// The radius must not be negative. For example:
///
/// ```dart
/// final a = circleArea(2);
/// print(a); // 12.566370614359172
/// ```
double circleArea(double radius) {
  return 3.141592653589793 * radius * radius;
}
```

Key points:

- Documentation comments support **Markdown**: backticks for code, `*italics*`, `**bold**`, lists, and fenced code blocks.
- Wrapping a name in square brackets, like `[radius]`, creates a **link** to that parameter, class, or function in the generated docs, and editors can jump to it.
- The first sentence becomes a short summary. Make it a complete sentence on its own line, followed by a blank `///` line before any longer description.
- Editors show these comments in hover tooltips and code completion popups.

Document classes and their members the same way:

```dart
/// A bank account that holds a [balance].
class Account {
  /// The current balance in dollars.
  double balance = 0;

  /// Adds [amount] to the [balance].
  ///
  /// Throws an [ArgumentError] if [amount] is negative.
  void deposit(double amount) {
    if (amount < 0) throw ArgumentError('amount must not be negative');
    balance += amount;
  }
}
```

(Classes are covered later in the course; here the focus is on the comments.)

A comment that documents a whole file (a *library*) goes at the very top, before any `import`, followed by the `library;` directive:

```dart
/// Utilities for working with circles.
library;

import 'dart:math';
```

> **Style tip:** Dart style prefers `///` over the older `/** ... */` form for documentation.

---

## 4.3 Generating Docs with `dart doc`

The SDK includes a tool that reads your `///` comments and builds a complete HTML documentation website, the same style used for the Dart API reference itself.

From the root of a project, run:

```bash
dart doc .
```

The output goes to the `doc/api` folder. Open `doc/api/index.html` in a browser to explore it. It lists every public library, class, function, and property, along with your comments.

Things to know:

- Only **public** members (names that do not begin with an underscore) are included by default.
- Broken `[references]` produce warnings, so `dart doc` also helps you spot typos in comments.
- Packages published to pub.dev get their documentation generated automatically from these same comments.

A well-documented public API is a sign of a well-made package. Even for private projects, writing a short `///` summary for each public item pays off quickly.

---

[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[5]-Variables-and-Type-Inference.md)
