[Previous](./[22]-Static-Members-and-Constants.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[24]-Namespaces-and-Autoloading.md)

*Object-Oriented Programming*

# Lesson 23 - Enums

## 23.1 What Are Enums?

Enums (introduced in PHP 8.1) let you define a fixed set of possible values for something, like a status or a category, instead of relying on loose strings or constants that could be mistyped.

---

## 23.2 Pure Enums

A pure enum's cases have no underlying value, just a name:

```php
<?php
enum Status {
    case Pending;
    case Active;
    case Closed;
}

function describe(Status $status): string {
    return match ($status) {
        Status::Pending => "Waiting to start",
        Status::Active  => "Currently in progress",
        Status::Closed  => "Finished",
    };
}

echo describe(Status::Active); // Currently in progress
```

---

## 23.3 Backed Enums

A backed enum ties each case to a scalar value (`string` or `int`), useful when the value needs to be stored in a database or sent over an API:

```php
<?php
enum Status: string {
    case Pending = 'pending';
    case Active  = 'active';
    case Closed  = 'closed';
}

echo Status::Active->value; // "active"

$status = Status::from('pending'); // Status::Pending
```

`from()` throws an error for an invalid value; `tryFrom()` returns `null` instead.

---

## 23.4 Enum Methods and Interfaces

Enums can define their own methods and implement interfaces, just like classes:

```php
<?php
enum Status: string {
    case Pending = 'pending';
    case Active  = 'active';
    case Closed  = 'closed';

    public function label(): string {
        return match ($this) {
            Status::Pending => 'Waiting',
            Status::Active  => 'In Progress',
            Status::Closed  => 'Done',
        };
    }
}

echo Status::Active->label(); // In Progress
```

---

[Previous](./[22]-Static-Members-and-Constants.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[24]-Namespaces-and-Autoloading.md)
