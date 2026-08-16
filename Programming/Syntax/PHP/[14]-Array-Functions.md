[Previous](./[13]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[15]-OOP-Classes-and-Objects.md)

*Data Structures*

# Lesson 14 - Array Functions

## 14.1 array_map

Applies a callback to every element and returns a new array with the results:

```php
<?php
$numbers = [1, 2, 3];
$doubled = array_map(fn($n) => $n * 2, $numbers);
// [2, 4, 6]
```

---

## 14.2 array_filter

Keeps only the elements that pass a callback's test:

```php
<?php
$numbers = [1, 2, 3, 4, 5, 6];
$evens = array_filter($numbers, fn($n) => $n % 2 === 0);
// [1 => 2, 3 => 4, 5 => 6] — keys are preserved
```

Note that `array_filter` preserves original keys, so you may want `array_values()` afterward to re-index the result.

---

## 14.3 array_reduce

Collapses an array down to a single value by repeatedly applying a callback:

```php
<?php
$numbers = [1, 2, 3, 4];
$sum = array_reduce($numbers, fn($carry, $n) => $carry + $n, 0);
// 10
```

The third argument (`0` above) is the starting value for `$carry`.

---

## 14.4 Sorting Arrays

```php
<?php
$nums = [3, 1, 2];
sort($nums);   // [1, 2, 3], re-indexes keys
rsort($nums);  // [3, 2, 1], descending

$ages = ["Sam" => 25, "Ann" => 30];
ksort($ages);  // sorts by key
asort($ages);  // sorts by value, preserving keys

$people = [["age" => 30], ["age" => 20]];
usort($people, fn($a, $b) => $a["age"] <=> $b["age"]); // custom sort
```

`usort()` combined with the spaceship operator from Lesson 7 is the standard way to sort by a custom rule.

---

[Previous](./[13]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[15]-OOP-Classes-and-Objects.md)
