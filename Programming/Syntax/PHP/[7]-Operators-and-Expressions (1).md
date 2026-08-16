[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators And Expressions

## 7.1 Arithmetic Operators

```php
<?php
$sum  = 5 + 2;  // 7
$diff = 5 - 2;  // 3
$prod = 5 * 2;  // 10
$quot = 5 / 2;  // 2.5
$mod  = 5 % 2;  // 1
$pow  = 5 ** 2; // 25
```

---

## 7.2 Comparison Operators (== vs ===)

`==` compares values after type juggling; `===` compares both value **and** type. Prefer `===` to avoid surprising conversions:

```php
<?php
var_dump(0 == "abc");  // false in PHP 8+ (was true before PHP 8)
var_dump("5" == 5);    // true — values match after conversion
var_dump("5" === 5);   // false — types differ
```

`!=` and `!==` are their negated counterparts.

---

## 7.3 Logical Operators

```php
<?php
$a = true;
$b = false;

var_dump($a && $b); // false — AND
var_dump($a || $b); // true  — OR
var_dump(!$a);      // false — NOT
```

`and`/`or` also exist as lower-precedence word versions, but `&&`/`||` are more common in practice.

---

## 7.4 The Spaceship Operator (<=>)

Returns `-1`, `0`, or `1` depending on whether the left side is less than, equal to, or greater than the right side. It's especially useful inside custom sort callbacks:

```php
<?php
echo 1 <=> 2; // -1
echo 2 <=> 2; // 0
echo 3 <=> 2; // 1
```

---

## 7.5 The Null Coalescing Operator (?? and ??=)

`??` returns its left operand if it's set and not `null`, otherwise the right operand — useful for defaults:

```php
<?php
$username = $_GET['user'] ?? 'guest';
```

`??=` assigns a value only if the variable is currently null or unset:

```php
<?php
$config['theme'] ??= 'light'; // sets 'light' only if not already set
```

---

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[8]-Conditionals.md)
