[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Your First Dart Program

Now that Dart is installed, it is time to write and run some code. This lesson covers the starting point of every Dart program, how to print output, two ways to run code, and what a real Dart project looks like on disk.

---

## 2.1 The `main()` Function

Every Dart program begins execution at a top-level function named `main()`. Without it, there is nothing to run.

```dart
void main() {
  print('Hello, Dart!');
}
```

Breaking this down:

- `void` is the return type. It means the function does not return a value.
- `main` is the required name.
- `()` is the (empty) parameter list.
- `{ ... }` is the function body. Each statement inside ends with a semicolon `;`.

A `main()` function can also accept command-line arguments as a list of strings:

```dart
void main(List<String> arguments) {
  print('Received ${arguments.length} argument(s): $arguments');
}
```

You will use this form in the command-line applications lesson later in the course. For now, `void main()` is all you need.

> **Note:** Dart is not whitespace-sensitive like Python. Curly braces and semicolons define structure, and indentation is purely for readability (the formatter in Lesson 3 handles it for you).

---

## 2.2 Printing with `print()`

`print()` writes a value to the console followed by a newline. It accepts any object and converts it to text by calling its `toString()` method.

```dart
void main() {
  print('Hello');          // Hello
  print(42);               // 42
  print(3.14);             // 3.14
  print(true);             // true
  print([1, 2, 3]);        // [1, 2, 3]
  print(null);             // null
}
```

You can insert values into text using **string interpolation** with `$`. This is covered fully in Lesson 7, but it is so common that a preview helps:

```dart
void main() {
  String name = 'Ana';
  int age = 20;
  print('My name is $name and I am $age years old.');
  print('Next year I will be ${age + 1}.');
}
```

Output:

```
My name is Ana and I am 20 years old.
Next year I will be 21.
```

Use `$name` for a simple variable and `${...}` for any larger expression.

---

## 2.3 Running with `dart run`

The quickest way to run a single file is to pass it to the `dart` command. Save the following as `hello.dart`:

```dart
void main() {
  print('Hello from a file!');
}
```

Then run it from the folder that contains the file:

```bash
dart run hello.dart
```

The shorter form works as well:

```bash
dart hello.dart
```

Both commands compile the file in memory using the Dart VM's JIT compiler and run it immediately. No separate build step is needed. This makes experimenting very fast.

To run a file that takes arguments, add them after the file name:

```bash
dart run args_demo.dart one two three
```

---

## 2.4 Creating a Project with `dart create`

A single file is fine for experiments, but real programs live in **projects**. The `dart create` command generates one with a sensible layout:

```bash
dart create my_app
```

This creates a folder called `my_app` and downloads any dependencies. Project names should use `lowercase_with_underscores`.

By default you get a **console application** template. Other templates are available with the `-t` option:

| Template | Command | Purpose |
|---|---|---|
| `console` (default) | `dart create -t console my_app` | Command-line application |
| `package` | `dart create -t package my_lib` | A reusable library to share |
| `server-shelf` | `dart create -t server-shelf my_server` | A web server using the `shelf` package |
| `web` | `dart create -t web my_site` | A browser application |

Run the project from inside its folder:

```bash
cd my_app
dart run
```

With no file name, `dart run` finds and runs the project's main entry point (`bin/my_app.dart`).

---

## 2.5 Anatomy of a Dart Project

After `dart create my_app`, the folder looks like this:

```
my_app/
├── bin/
│   └── my_app.dart          # entry point containing main()
├── lib/
│   └── my_app.dart          # reusable code that others (and bin/) can import
├── test/
│   └── my_app_test.dart     # automated tests
├── pubspec.yaml             # project description and dependencies
├── pubspec.lock             # exact dependency versions (generated)
├── analysis_options.yaml    # analyzer and lint configuration
├── CHANGELOG.md             # history of changes
└── README.md                # project documentation
```

What each part is for:

- **`bin/`** holds the files you *run*. Each file here with a `main()` is an executable entry point.
- **`lib/`** holds the code you *share*. Code here can be imported by `bin/`, by tests, and by other packages.
- **`test/`** holds your automated tests (see the unit testing lesson).
- **`pubspec.yaml`** is the project's manifest: name, version, SDK constraint, and dependencies.
- **`pubspec.lock`** is generated by `dart pub get` and records the exact versions used, so builds are repeatable.
- **`analysis_options.yaml`** tells the analyzer which lint rules to enforce.

A minimal `pubspec.yaml` looks like this:

```yaml
name: my_app
description: A sample command-line application.
version: 1.0.0

environment:
  sdk: ^3.0.0

dependencies:
  # none yet

dev_dependencies:
  lints: ^4.0.0
  test: ^1.24.0
```

The `bin/my_app.dart` file imports the library from `lib/` using a `package:` URL:

```dart
import 'package:my_app/my_app.dart' as my_app;

void main(List<String> arguments) {
  print('Hello world: ${my_app.calculate()}!');
}
```

Dependency versions in a freshly generated project will be newer than the ones shown here; the exact numbers do not matter for learning.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[3]-Development-Environment.md)
