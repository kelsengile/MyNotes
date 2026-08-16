[Previous](./[17]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[19]-Interfaces-and-Abstract-Classes.md)

*Object-Oriented Programming*

# Lesson 18 - Encapsulation And Visibility

## 18.1 What is Encapsulation?

Encapsulation means keeping an object's internal details hidden and only exposing what's necessary through a controlled interface. This protects data from being changed in unexpected ways from outside the class.

---

## 18.2 public, private, and protected

- `public` — accessible from anywhere.
- `protected` — accessible within the class and its subclasses only.
- `private` — accessible only within the exact class it's defined in.

```php
<?php
class BankAccount {
    private float $balance = 0;

    public function deposit(float $amount): void {
        $this->balance += $amount;
    }

    public function getBalance(): float {
        return $this->balance;
    }
}

$account = new BankAccount();
$account->deposit(100);
echo $account->getBalance(); // 100
// $account->balance would trigger a visibility error — it's private
```

---

## 18.3 Getters and Setters

Since private properties can't be accessed directly from outside, classes typically expose controlled **getter** and **setter** methods, which can also add validation:

```php
<?php
class Product {
    private float $price;

    public function setPrice(float $price): void {
        if ($price < 0) {
            throw new \InvalidArgumentException("Price cannot be negative.");
        }
        $this->price = $price;
    }

    public function getPrice(): float {
        return $this->price;
    }
}
```

---

## 18.4 readonly Properties

PHP 8.1 introduced `readonly` properties, which can be set once (typically in the constructor) and never modified afterward:

```php
<?php
class Point {
    public function __construct(
        public readonly float $x,
        public readonly float $y,
    ) {}
}

$point = new Point(1.5, 2.5);
// $point->x = 3; // Error: cannot modify a readonly property
```

`readonly` is a lightweight alternative to writing a getter with no setter, useful for values that shouldn't change after creation.

---

[Previous](./[17]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[19]-Interfaces-and-Abstract-Classes.md)
