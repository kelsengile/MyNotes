[Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[2]-Your-First-Program.md)

*Getting Started*

# Lesson 1 - Installing the Dart SDK

Before you can write and run Dart programs, you need the tools that understand the language. In this lesson you will learn what the Dart SDK contains, how to install it on your operating system, and how to confirm that everything works. You will also meet DartPad, a browser-based playground that needs no installation at all.

---

## 1.1 What Is the Dart SDK?

The **Dart SDK** (Software Development Kit) is a bundle of everything needed to write, run, test, and ship Dart code. When you install it, you get:

| Component | What it does |
|---|---|
| `dart` command | The single entry point for almost every task (`dart run`, `dart test`, `dart format`, ...) |
| **Dart VM** | Runs your code directly with a JIT (just-in-time) compiler, giving fast edit-and-run cycles |
| **Compilers** | Produce native executables (AOT), JavaScript, or WebAssembly for release builds |
| **Core libraries** | `dart:core`, `dart:async`, `dart:math`, `dart:io`, `dart:convert`, and more |
| **Analyzer** | Finds errors and style problems before you run your code |
| **Formatter** | Automatically lays out your code in the standard style |
| **`pub` package manager** | Downloads libraries from [pub.dev](https://pub.dev) |
| **Documentation generator** | Builds HTML docs from your comments |

You normally interact with all of these through the one `dart` command.

---

## 1.2 Installing on Windows, macOS, and Linux

Installation steps change from time to time, so treat the official page, [https://dart.dev/get-dart](https://dart.dev/get-dart), as the source of truth. The usual methods are shown below.

### Windows

The easiest option is the [Chocolatey](https://chocolatey.org) package manager. Run this in an **administrator** terminal:

```bash
choco install dart-sdk
```

To upgrade later:

```bash
choco upgrade dart-sdk
```

Alternatively, download the SDK as a ZIP file from the official site, extract it somewhere permanent (for example `C:\dart-sdk`), and add its `bin` folder to your `PATH` environment variable.

### macOS

Use [Homebrew](https://brew.sh):

```bash
brew tap dart-lang/dart
brew install dart
```

To upgrade later:

```bash
brew upgrade dart
```

### Linux (Debian / Ubuntu)

Add Google's package repository, then install with `apt`:

```bash
sudo apt-get update
sudo apt-get install apt-transport-https
wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub \
  | sudo gpg --dearmor -o /usr/share/keyrings/dart.gpg
echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' \
  | sudo tee /etc/apt/sources.list.d/dart_stable.list
sudo apt-get update
sudo apt-get install dart
```

The package installs to `/usr/lib/dart`. Add it to your `PATH` (for example in `~/.bashrc`):

```bash
export PATH="$PATH:/usr/lib/dart/bin"
```

Other Linux distributions can use the downloadable ZIP archive in the same way as on Windows.

> **Tip:** If you also plan to use Flutter, you do not need a separate install. See the next sub-lesson.

---

## 1.3 Dart vs Flutter: Which Do You Need?

These two names are often confused:

- **Dart** is the *programming language* and its tools.
- **Flutter** is a *UI framework* (a library of widgets and tools for building apps) written in Dart.

| If you want to... | Install |
|---|---|
| Learn the Dart language | Dart SDK |
| Write command-line tools or servers | Dart SDK |
| Build mobile, desktop, or web apps with Flutter | Flutter SDK |

The **Flutter SDK already bundles a copy of Dart**. If you install Flutter, the `dart` command is available too (inside Flutter's `bin` folder). If you have both installed separately, make sure you know which `dart` your terminal is using:

```bash
which dart      # macOS / Linux
where dart      # Windows
```

This course teaches the Dart language itself, so the standalone Dart SDK is all you need.

---

## 1.4 Verifying Your Installation

Open a **new** terminal window (an already-open one will not see the updated `PATH`) and run:

```bash
dart --version
```

You should see output similar to:

```
Dart SDK version: 3.x.x (stable) on "windows_x64"
```

The exact version and platform will differ. If you see "command not found" or "not recognized", the SDK's `bin` folder is not on your `PATH`; revisit the PATH step for your system.

A few other useful checks:

```bash
dart --help          # lists every available command
dart info            # prints diagnostic information about your setup
```

---

## 1.5 Using DartPad in the Browser

**DartPad** at [https://dartpad.dev](https://dartpad.dev) is a free online editor that compiles and runs Dart in your browser. There is nothing to install and no account is required, which makes it ideal for:

- Trying out the examples in this course
- Sharing a snippet with someone else (each pad has a shareable link)
- Quickly testing an idea without creating a project

A DartPad program has the same shape as any Dart program. You write a `main()` function (covered in the next lesson), press **Run**, and the output appears in the console panel:

```dart
void main() {
  print('Hello from DartPad!');
}
```

DartPad has limits: it cannot read local files, and some libraries (such as `dart:io`) are unavailable because the code runs in a browser. For everything else in the early lessons, it works exactly like the full SDK.

---

[Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[2]-Your-First-Program.md)
