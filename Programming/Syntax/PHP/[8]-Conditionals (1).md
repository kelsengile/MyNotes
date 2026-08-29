[Previous](./%5B7%5D-Operators-and-Expressions%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./%5B9%5D-Loops%20%281%29.md)

*Core Syntax*

# Lesson 8 - Conditionals

## 8.1 if, elseif, else

```php
<?php
$score = 72;

if ($score >= 90) {
    echo "A";
} elseif ($score >= 70) {
    echo "B";
} else {
    echo "C";
}
```

Conditions are evaluated top to bottom; the first `true` branch runs and the rest are skipped.

---

## 8.2 switch Statements

`switch` compares one value against several possibilities using loose (`==`) comparison:

```php
<?php
switch ($role) {
    case 'admin':
        echo "Full access";
        break;
    case 'editor':
        echo "Can edit content";
        break;
    default:
        echo "Read-only access";
}
```

Don't forget `break` — without it, execution "falls through" into the next case.

---

## 8.3 The match Expression

Introduced in PHP 8, `match` is a more concise, strict-comparison (`===`) alternative to `switch`. It's an expression, so it returns a value directly and needs no `break`:

```php
<?php
$access = match ($role) {
    'admin', 'owner' => "Full access",
    'editor' => "Can edit content",
    default => "Read-only access",
};
```

Unlike `switch`, `match` throws an `UnhandledMatchError` if no case matches and there's no `default`.

---

## 8.4 Ternary and Short Ternary Operators

The ternary operator condenses a simple if/else into one line:

```php
<?php
$label = ($age >= 18) ? "Adult" : "Minor";
```

The short ternary `?:` returns the left operand if it's truthy, otherwise the right:

```php
<?php
$name = $inputName ?: "Anonymous";
```

For handling `null` specifically, prefer the null coalescing operator (`??`) from the previous lesson instead.

---

[Previous](./%5B7%5D-Operators-and-Expressions%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./%5B9%5D-Loops%20%281%29.md)
