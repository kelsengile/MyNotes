[Previous](./%5B8%5D-Conditionals%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[10]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops

## 9.1 for Loops

Best when you know how many iterations you need:

```php
<?php
for ($i = 0; $i < 5; $i++) {
    echo $i . " ";
}
// 0 1 2 3 4
```

---

## 9.2 while and do-while Loops

`while` checks its condition before each iteration:

```php
<?php
$count = 0;
while ($count < 3) {
    echo $count;
    $count++;
}
```

`do-while` checks after each iteration, so the body always runs at least once:

```php
<?php
$count = 0;
do {
    echo $count;
    $count++;
} while ($count < 3);
```

---

## 9.3 foreach Loops

The most common loop for arrays — it iterates over each element directly:

```php
<?php
$fruits = ["apple", "banana", "cherry"];
foreach ($fruits as $fruit) {
    echo $fruit . " ";
}

$prices = ["apple" => 1.5, "banana" => 0.5];
foreach ($prices as $name => $price) {
    echo "$name: $price ";
}
```

We'll cover arrays themselves in detail in the Data Structures section.

---

## 9.4 break and continue

`break` exits a loop early; `continue` skips to the next iteration:

```php
<?php
foreach ([1, 2, 3, 4, 5] as $num) {
    if ($num === 3) {
        continue; // skip 3
    }
    if ($num === 5) {
        break; // stop entirely at 5
    }
    echo $num . " ";
}
// 1 2 4
```

---

[Previous](./%5B8%5D-Conditionals%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[10]-Functions-and-Scope.md)
