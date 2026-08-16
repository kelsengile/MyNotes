[Previous](./[24]-Namespaces-and-Autoloading.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[26]-Generators-and-Iterators.md)

*Advanced Language Features*

# Lesson 25 - Closures And Anonymous Functions

## 25.1 Anonymous Functions

An anonymous function is a function without a name, often used inline as a callback:

```php
<?php
$square = function ($n) {
    return $n * $n;
};

echo $square(5); // 25

$numbers = [1, 2, 3];
$squares = array_map(function ($n) {
    return $n * $n;
}, $numbers);
```

---

## 25.2 Closures and the use Keyword

A closure is an anonymous function that "closes over" — captures — variables from the surrounding scope. By default, PHP captures those variables by value, so you must opt in explicitly with `use`:

```php
<?php
$taxRate = 0.08;

$addTax = function ($price) use ($taxRate) {
    return $price * (1 + $taxRate);
};

echo $addTax(100); // 108
```

To modify the outer variable itself (not just read it), capture by reference with `use (&$variable)`:

```php
<?php
$total = 0;
$add = function ($amount) use (&$total) {
    $total += $amount;
};

$add(10);
$add(5);
echo $total; // 15
```

---

## 25.3 Arrow Functions Revisited

Arrow functions (`fn`), introduced back in Lesson 10, are closures too — but they automatically capture outer variables by value without needing `use`:

```php
<?php
$taxRate = 0.08;
$addTax = fn($price) => $price * (1 + $taxRate); // no "use" needed
```

Arrow functions are limited to a single expression, so full closures are still needed for multi-line logic or reference captures.

---

## 25.4 Passing Closures as Callbacks

Closures are commonly passed as arguments to functions that expect a callback, such as the array functions from Lesson 14:

```php
<?php
$people = [["name" => "Kim", "age" => 25], ["name" => "Alex", "age" => 30]];

usort($people, function ($a, $b) {
    return $a["age"] <=> $b["age"];
});
```

---

[Previous](./[24]-Namespaces-and-Autoloading.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[26]-Generators-and-Iterators.md)
