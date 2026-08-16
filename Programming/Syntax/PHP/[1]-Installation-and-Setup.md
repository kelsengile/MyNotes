[Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[2]-Running-PHP-Code.md)

*Getting Started*

# Lesson 1 - Installation And Setup

## 1.1 Why Install PHP Locally

To write and test PHP code, you need a PHP interpreter installed on your machine. Working locally lets you run scripts instantly, experiment freely, and catch mistakes before deploying anything to a real server.

---

## 1.2 Installing PHP

**Windows**
- Download a PHP build from [windows.php.net](https://windows.php.net/download/), extract it, and add the folder to your system `PATH`.
- Alternatively, use a bundle like XAMPP or Laragon, which installs PHP alongside Apache and MySQL.

**macOS**
- The easiest route is [Homebrew](https://brew.sh):
```bash
brew install php
```

**Linux (Debian/Ubuntu)**
```bash
sudo apt update
sudo apt install php
```

Most Linux distributions ship PHP through their package manager, so an update-and-install pair is usually all you need.

---

## 1.3 Verifying Your Installation

Open a terminal and check the installed version:

```bash
php -v
```

You should see output similar to:

```
PHP 8.3.0 (cli) (built: ...) 
```

If the command isn't found, double-check that PHP's folder was added to your system `PATH`.

---

## 1.4 Choosing an Editor

Any text editor works, but editors with PHP-aware features make learning easier. Popular choices include **VS Code** (with the PHP Intelephense extension), **PhpStorm**, and **Sublime Text**. Pick one, install a PHP syntax-highlighting extension if it doesn't already have one, and you're ready to start writing code.

---

[Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[2]-Running-PHP-Code.md)
