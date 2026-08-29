[Previous](./%5B5%5D-Variables-and-Data-Types%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./%5B7%5D-Operators-and-Expressions%20%281%29.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings And Booleans

## 6.1 Working with Integers and Floats

Integers (`int`) are whole numbers; floats (`float`) hold decimals:

```php
<?php
$count = 10;      // int
$price = 9.99;    // float
```

Dividing two integers can still produce a float if the result isn't whole:

```php
<?php
$result = 7 / 2; // 3.5 (float)
```

---

## 6.2 Arithmetic and Number Functions

Standard operators work as expected: `+`, `-`, `*`, `/`, `%` (modulo), and `**` (exponent). PHP also ships with useful number functions:

```php
<?php
echo round(3.14159, 2); // 3.14
echo abs(-5);            // 5
echo max(3, 7, 2);        // 7
echo intdiv(7, 2);        // 3 (integer division)
```

---

## 6.3 Strings: Single vs Double Quotes

Both create strings, but they behave differently:

```php
<?php
$name = "Sam";
echo 'Hello, $name';  // Hello, $name (literal, no interpolation)
echo "Hello, $name";  // Hello, Sam (variables are interpolated)
```

Single quotes are slightly faster since PHP doesn't scan them for variables, so use them for plain text and double quotes when you need interpolation.

---

## 6.4 Booleans and Truthy/Falsy Values

A `bool` is either `true` or `false`. When PHP needs a boolean but gets another type, it converts using these rules — the following are all treated as `false`:

```
false, 0, 0.0, "", "0", [], null
```

Everything else is considered `true`. This matters heavily once we cover conditionals:

```php
<?php
if ("0") {
    echo "truthy";
} else {
    echo "falsy"; // this runs — "0" is falsy
}
```

---

[Previous](./%5B5%5D-Variables-and-Data-Types%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./%5B7%5D-Operators-and-Expressions%20%281%29.md)
