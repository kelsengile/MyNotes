[Previous](./[12]-Error-and-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[14]-Array-Functions.md)

*Data Structures*

# Lesson 13 - Arrays

## 13.1 Indexed Arrays

Indexed arrays store an ordered list of values, automatically numbered starting at `0`:

```php
<?php
$colors = ["red", "green", "blue"];
echo $colors[0]; // red

$colors[] = "yellow"; // appends to the end
```

---

## 13.2 Associative Arrays

Associative arrays use custom keys instead of numeric indexes, mapping keys to values:

```php
<?php
$person = [
    "name" => "Nora",
    "age"  => 29,
];

echo $person["name"]; // Nora
$person["email"] = "nora@example.com"; // add a new key
```

---

## 13.3 Multidimensional Arrays

Arrays can contain other arrays, useful for representing structured or tabular data:

```php
<?php
$students = [
    ["name" => "Ana", "grade" => 90],
    ["name" => "Leo", "grade" => 85],
];

echo $students[0]["name"]; // Ana

foreach ($students as $student) {
    echo $student["name"] . ": " . $student["grade"] . " ";
}
```

---

## 13.4 Common Array Operations

```php
<?php
$fruits = ["apple", "banana"];

count($fruits);              // 2
in_array("apple", $fruits);  // true
array_key_exists(0, $fruits); // true
array_push($fruits, "cherry"); // adds "cherry"
array_pop($fruits);           // removes and returns the last element
array_merge($fruits, ["kiwi"]); // combines two arrays
```

Arrays are one of PHP's most-used structures — the next lesson covers functions that transform and process them in bulk.

---

[Previous](./[12]-Error-and-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[14]-Array-Functions.md)
