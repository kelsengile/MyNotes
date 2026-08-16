[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[12]-Error-and-Exception-Handling.md)

*Core Syntax*

# Lesson 11 - String Formatting

## 11.1 String Interpolation

Double-quoted strings can embed variables directly. Wrap more complex expressions like array access or object properties in curly braces for clarity:

```php
<?php
$user = ["name" => "Lee"];
echo "Welcome, {$user['name']}!"; // Welcome, Lee!
```

---

## 11.2 Heredoc and Nowdoc

Heredoc behaves like a double-quoted string (variables are interpolated) but is easier to read for multi-line text:

```php
<?php
$name = "Priya";
$message = <<<EOT
Hello, $name!
This is a multi-line message.
EOT;
```

Nowdoc behaves like a single-quoted string (no interpolation), using single quotes around the identifier:

```php
<?php
$template = <<<'EOT'
No $variables are parsed here.
EOT;
```

---

## 11.3 Common String Functions

```php
<?php
echo strlen("hello");           // 5
echo strtoupper("hello");       // HELLO
echo str_replace("a", "o", "banana"); // bonono
echo trim("  hi  ");            // "hi"
echo substr("hello world", 0, 5); // "hello"
echo str_contains("hello", "ell"); // true
```

---

## 11.4 sprintf and Formatting Output

`sprintf()` builds formatted strings using placeholders — useful for aligning numbers, padding, or controlling decimal places:

```php
<?php
$price = 9.5;
echo sprintf("Total: $%.2f", $price); // Total: $9.50

$id = 7;
echo sprintf("Order #%04d", $id); // Order #0007
```

`printf()` works the same way but prints directly instead of returning a string.

---

[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[12]-Error-and-Exception-Handling.md)
