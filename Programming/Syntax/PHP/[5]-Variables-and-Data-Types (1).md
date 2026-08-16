[Previous](./[4]-php-ini-and-Configuration.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables And Data Types

## 5.1 Declaring Variables

PHP variables start with a `$` sign and don't need a declared type:

```php
<?php
$name = "Ada";
$age = 36;
```

Variables are assigned with `=` and can be reassigned at any time, even to a value of a different type.

---

## 5.2 Variable Naming Rules

- Must start with a letter or underscore, followed by letters, numbers, or underscores.
- Case-sensitive: `$Name` and `$name` are different variables.
- By convention, PHP variables use `camelCase` or `snake_case`.

```php
<?php
$firstName = "Grace"; // valid, camelCase
$_temp = 42;           // valid, starts with underscore
```

---

## 5.3 PHP's Data Types Overview

PHP has eight main types:

| Type | Example |
|---|---|
| `int` | `42` |
| `float` | `3.14` |
| `string` | `"hello"` |
| `bool` | `true` / `false` |
| `array` | `[1, 2, 3]` |
| `object` | instance of a class |
| `null` | `null` |
| `callable` / `resource` | functions, file handles, etc. |

We'll explore numbers, strings, and booleans in depth next lesson, and arrays and objects later in the course.

---

## 5.4 Type Juggling and Loose Typing

PHP is loosely typed by default — it converts values between types automatically when needed:

```php
<?php
$total = "5" + 3; // 8 (string "5" is converted to int)
$msg   = "Age: " . 25; // "Age: 25" (int converted to string)
```

This flexibility is convenient but can cause subtle bugs, which is why later lessons cover strict comparison and strict typing.

---

## 5.5 Checking and Converting Types

Use `gettype()` to inspect a value's type, and `var_dump()` to see its type and value together:

```php
<?php
$value = "42";
echo gettype($value); // "string"
var_dump($value);     // string(2) "42"
```

To convert explicitly, cast the value:

```php
<?php
$number = (int) "42"; // 42, as an integer
```

---

[Previous](./[4]-php-ini-and-Configuration.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)
