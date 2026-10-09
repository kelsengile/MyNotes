[Previous](./[2]-Your-First-Program.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[4]-Comments-and-Documentation.md)

*Getting Started*

# Lesson 3 - Your Development Environment

Dart's tooling is one of its biggest strengths. A good editor, the analyzer, the formatter, and the debugger will catch mistakes early and keep your code tidy. This lesson shows how to set them up and use them.

---

## 3.1 Choosing an Editor or IDE

You can write Dart in any text editor, but these options provide the best experience:

| Tool | Notes |
|---|---|
| **Visual Studio Code** | Free and lightweight. Install the official **Dart** extension (which also includes Flutter support when you install the **Flutter** extension). |
| **IntelliJ IDEA / Android Studio** | Full-featured IDEs. Install the **Dart** plugin (and the **Flutter** plugin for Flutter work). |
| **DartPad** | Browser-only playground, good for quick experiments (see Lesson 1). |

Whichever you choose, the Dart plugin gives you:

- **Code completion** as you type
- **Inline errors and warnings** with quick fixes
- **Go to definition** and **find references**
- **Automatic formatting** on save
- **Run and debug** buttons next to `main()`

All of these features are powered by the same **Dart analysis server** that ships with the SDK, so the experience is consistent across editors.

**Setting up VS Code (quick steps):**

1. Install VS Code.
2. Open the Extensions panel and search for **Dart**.
3. Install the extension published by *Dart Code*.
4. Open a folder containing a Dart project, or run **Dart: New Project** from the command palette.

---

## 3.2 The Dart Analyzer and Linter

The **analyzer** reads your code without running it and reports problems. You see its results as red or yellow squiggles in your editor, and you can run it manually:

```bash
dart analyze
```

Example output:

```
Analyzing my_app...

  error - bin/my_app.dart:3:15 - A value of type 'String' can't be assigned
          to a variable of type 'int'. - invalid_assignment
  info - bin/my_app.dart:5:3 - Don't invoke 'print' in production code.
         - avoid_print

2 issues found.
```

Issues come in three severities:

| Severity | Meaning |
|---|---|
| **error** | The code is invalid and will not run (for example, a type mismatch). |
| **warning** | The code is valid but probably wrong (for example, an unused variable). |
| **info** | A suggestion about style or best practice, often from a *lint rule*. |

The **linter** is the part of the analyzer that checks for style and best-practice issues using a set of named *lint rules* such as `avoid_print` or `prefer_const_constructors`. Which rules are active is controlled by a configuration file (next sub-lesson).

Run `dart analyze --fatal-infos` in automated builds if you want even info-level issues to fail the check.

---

## 3.3 `analysis_options.yaml` and Lint Rules

The file `analysis_options.yaml` sits at the root of your project and configures the analyzer. A project made with `dart create` includes one like this:

```yaml
# Defines a default set of lint rules enforced for projects at pub.dev
include: package:lints/recommended.yaml

# Uncomment the following section to specify additional rules.

# linter:
#   rules:
#     - camel_case_types

# analyzer:
#   exclude:
#     - path/to/excluded/files/**
```

- `include:` pulls in a ready-made rule set. `package:lints/recommended.yaml` is the baseline recommended by the Dart team. `package:lints/core.yaml` is a smaller set, and `package:flutter_lints/flutter.yaml` is used in Flutter projects.
- `linter: rules:` lets you switch individual rules on or off.
- `analyzer:` configures the analyzer itself (excluded files, error severities, strict modes).

An example with some customization:

```yaml
include: package:lints/recommended.yaml

analyzer:
  language:
    strict-casts: true
    strict-inference: true
    strict-raw-types: true
  exclude:
    - build/**

linter:
  rules:
    avoid_print: true
    prefer_single_quotes: true
    prefer_final_locals: true
```

The three **strict modes** make the type checker stricter:

- `strict-casts` disallows implicit downcasts from `dynamic`.
- `strict-inference` reports places where Dart had to fall back to `dynamic` because it could not infer a type.
- `strict-raw-types` flags generic types written without type arguments (such as `List` instead of `List<int>`).

You can also disable a rule for a single line or file with a special comment:

```dart
// ignore: avoid_print
print('debug output');
```

```dart
// ignore_for_file: avoid_print
```

Use ignores sparingly; they hide useful warnings.

---

## 3.4 `dart format` and Code Style

The **formatter** rewrites your code into the standard Dart layout so that everyone's code looks the same and style arguments disappear.

```bash
dart format .                 # format every file in the current folder tree
dart format lib/my_app.dart   # format one file
```

To check formatting without changing files (useful in automated builds):

```bash
dart format --output=none --set-exit-if-changed .
```

Before formatting:

```dart
void main(){var x=5;
  if(x>3){print('big');}else{print('small');}}
```

After formatting:

```dart
void main() {
  var x = 5;
  if (x > 3) {
    print('big');
  } else {
    print('small');
  }
}
```

Good to know:

- The formatter adds trailing commas where lists and argument lists are split over multiple lines, and a trailing comma you write yourself tells it to keep one item per line.
- Newer Dart versions (3.7 and later) use an updated "tall" style and let you set the line width in `analysis_options.yaml`:

```yaml
formatter:
  page_width: 100
```

- Most editors can run the formatter every time you save. In VS Code, enable **Format On Save** in settings.

---

## 3.5 Debugging and DevTools

A **debugger** lets you pause your program and inspect it while it runs.

**In VS Code or IntelliJ:**

1. Click in the gutter next to a line number to set a **breakpoint** (a red dot).
2. Press **F5** (VS Code) or click the debug icon to start debugging.
3. When execution reaches the breakpoint it pauses. You can inspect variables, then **step over**, **step into**, **step out**, or **continue**.

You can also pause programmatically using `dart:developer`:

```dart
import 'dart:developer';

void main() {
  var total = 0;
  for (var i = 1; i <= 5; i++) {
    total += i;
    if (i == 3) {
      debugger(); // pauses here when a debugger is attached
    }
  }
  print(total);
}
```

**Dart DevTools** is a suite of browser-based tools for performance profiling, memory inspection, and debugging. Start it from the terminal:

```bash
dart devtools
```

To connect DevTools to a running program, start the program with the VM service enabled:

```bash
dart run --enable-vm-service bin/my_app.dart
```

The program prints a URL that you can paste into DevTools. Debugging is covered in more depth in its own lesson later in the course.

---

[Previous](./[2]-Your-First-Program.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[4]-Comments-and-Documentation.md)
