[Previous](./%5B9%5D-Loops%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Functions And Scope

## 10.1 Defining and Calling Functions

```php
<?php
function greet($name) {
    return "Hello, $name!";
}

echo greet("Mira"); // Hello, Mira!
```

---

## 10.2 Parameters, Default Arguments, and Type Hints

Parameters can have default values and optional type declarations:

```php
<?php
function greet(string $name, string $greeting = "Hello"): string {
    return "$greeting, $name!";
}

echo greet("Ravi");          // Hello, Ravi!
echo greet("Ravi", "Hi");    // Hi, Ravi!
```

Type declarations aren't required in PHP, but they catch mistakes early and make code self-documenting. We'll go deeper on this in the Type Declarations lesson later in the course.

---

## 10.3 Variadic Functions

Use `...` to accept any number of arguments as an array:

```php
<?php
function total(...$numbers) {
    return array_sum($numbers);
}

echo total(1, 2, 3, 4); // 10
```

---

## 10.4 Variable Scope

Variables defined inside a function are local to it and don't exist outside:

```php
<?php
function setName() {
    $name = "Local";
}
setName();
echo $name; // Error: undefined variable $name
```

To use an outer variable inside a function, pass it in as a parameter rather than relying on `global`, which is best avoided in modern code.

---

## 10.5 Arrow Functions (fn)

Arrow functions are a compact syntax for simple one-expression functions, and automatically capture variables from the surrounding scope:

```php
<?php
$tax = 0.08;
$addTax = fn($price) => $price * (1 + $tax);

echo $addTax(100); // 108
```

We'll compare arrow functions with full closures in the Closures lesson later on.

---

[Previous](./%5B9%5D-Loops%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[11]-String-Formatting.md)
