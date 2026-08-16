[Previous](./[26]-Generators-and-Iterators.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[28]-Attributes.md)

*Advanced Language Features*

# Lesson 27 - Type Declarations And Strict Typing

## 27.1 Type Declarations for Parameters and Returns

You met basic type hints back in Lesson 10. PHP supports declaring types for parameters, return values, and properties:

```php
<?php
function add(int $a, int $b): int {
    return $a + $b;
}

class Product {
    public string $name;
    public float $price;
}
```

If a caller passes a value of the wrong type, PHP throws a `TypeError` (in strict mode) or attempts to convert it (in coercive mode — see 27.4).

---

## 27.2 Nullable and Union Types

A `?` before a type allows `null` in addition to the declared type:

```php
<?php
function findUser(int $id): ?string {
    return $id === 1 ? "Alice" : null;
}
```

Union types (PHP 8+) allow a value to be one of several specific types:

```php
<?php
function process(int|string $id): void {
    echo "Processing ID: $id";
}
```

---

## 27.3 declare(strict_types=1)

By default, PHP tries to convert mismatched types automatically ("coercive typing"). Adding this line as the very first statement in a file switches that file to **strict typing**:

```php
<?php
declare(strict_types=1);

function add(int $a, int $b): int {
    return $a + $b;
}

add("5", 3); // TypeError — strings are no longer auto-converted to int
```

---

## 27.4 Strict vs Coercive Typing

| | Coercive (default) | Strict (`declare(strict_types=1)`) |
|---|---|---|
| `add("5", 3)` | Converts `"5"` to `5`, returns `8` | Throws `TypeError` |
| Where it applies | Same file only | Same file only — it affects calls *made from* that file |
| Recommended for | Rarely | Most modern projects, to catch bugs early |

Most style guides recommend enabling strict types in every new file, since it turns silent type-conversion bugs into immediate, visible errors.

---

[Previous](./[26]-Generators-and-Iterators.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[28]-Attributes.md)
