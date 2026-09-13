[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)

# Composer

Composer is the standard dependency manager for PHP. It installs libraries on a per-project basis, tracks them in a `composer.json` file, and generates an autoloader so your code can use them without manual `require` statements.

Download [https://getcomposer.org/download/](https://getcomposer.org/download/)

---

## What Is Composer?

Rather than installing PHP libraries system-wide, Composer installs them into a `vendor/` folder inside your project. Each project can then depend on different versions of the same library without conflicts.

---

## Core Commands

```bash
composer init                    # Create a new composer.json interactively
composer require <package>       # Add a dependency and install it
composer require --dev <package> # Add a development-only dependency
composer install                 # Install everything listed in composer.json
composer update                  # Update dependencies to newer allowed versions
composer remove <package>        # Remove a dependency
```

---

## composer.json and composer.lock

`composer.json` declares which packages your project needs and the version ranges allowed. `composer.lock` records the exact versions actually installed — commit both so every environment builds identically.

```json
{
  "require": {
    "monolog/monolog": "^3.0"
  }
}
```

---

## Autoloading

Composer generates `vendor/autoload.php`, which automatically loads any installed package's classes:

```php
require 'vendor/autoload.php';

$log = new Monolog\Logger('name');
```

---

## Example Walkthrough

```bash
composer init --no-interaction
composer require monolog/monolog
```

Creates a new `composer.json` and installs the Monolog logging library into `vendor/`.

[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)
