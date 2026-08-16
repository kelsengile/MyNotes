[Previous](./[2]-Running-PHP-Code.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[4]-php-ini-and-Configuration.md)

*Getting Started*

# Lesson 3 - Composer And Dependency Management

## 3.1 What is Composer?

Composer is PHP's dependency manager. It lets you pull in third-party libraries, manage version constraints, and autoload your own code, all without manually downloading files. Almost every modern PHP project (Laravel, Symfony, and countless packages) relies on it.

---

## 3.2 Installing Composer

Follow the installer at [getcomposer.org](https://getcomposer.org/download/) for your operating system. Once installed, confirm it works:

```bash
composer --version
```

---

## 3.3 composer.json and Requiring Packages

Every Composer-managed project has a `composer.json` file describing its dependencies. You can create one interactively:

```bash
composer init
```

To add a package (for example, a date-formatting library):

```bash
composer require nesbot/carbon
```

This downloads the package into a `vendor/` folder and records the dependency (and its version constraint) in `composer.json`.

---

## 3.4 Autoloading with Composer

Composer generates an autoloader that automatically loads classes as you use them, so you never need long chains of `require` statements. Include it once at the top of your entry file:

```php
<?php
require 'vendor/autoload.php';
```

From that point on, any installed package's classes — and your own, if configured via PSR-4 — are available wherever you need them. We'll cover PSR-4 autoloading for your own code in the Namespaces lesson later in this course.

---

[Previous](./[2]-Running-PHP-Code.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[4]-php-ini-and-Configuration.md)
