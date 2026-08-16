[Previous](./[18]-Encapsulation-and-Visibility.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[20]-Traits.md)

*Object-Oriented Programming*

# Lesson 19 - Interfaces And Abstract Classes

## 19.1 Interfaces

An interface defines a contract — a set of methods a class must implement — without providing any implementation itself:

```php
<?php
interface Payable {
    public function pay(float $amount): bool;
}

class CreditCard implements Payable {
    public function pay(float $amount): bool {
        echo "Charged $$amount to credit card.";
        return true;
    }
}
```

Any class that `implements Payable` is guaranteed to have a `pay()` method, which lets other code rely on that contract without caring about the concrete class.

---

## 19.2 Abstract Classes

An abstract class can define some shared implementation while still requiring subclasses to implement certain methods. It cannot be instantiated directly:

```php
<?php
abstract class Shape {
    abstract public function area(): float;

    public function describe(): string {
        return "This shape has an area of " . $this->area();
    }
}

class Circle extends Shape {
    public function __construct(private float $radius) {}

    public function area(): float {
        return pi() * $this->radius ** 2;
    }
}

$circle = new Circle(3);
echo $circle->describe();
```

---

## 19.3 Interfaces vs Abstract Classes

| | Interface | Abstract Class |
|---|---|---|
| Method bodies | None allowed | Can mix abstract and concrete methods |
| Properties | Constants only (no state) | Can hold properties |
| Multiple inheritance | A class can implement several | A class can extend only one |
| Use when | Defining a capability/contract | Sharing common base behavior |

---

## 19.4 Implementing Multiple Interfaces

Unlike classes, a class can implement as many interfaces as it needs, separated by commas:

```php
<?php
interface Printable {
    public function printLabel(): void;
}

class Invoice implements Payable, Printable {
    public function pay(float $amount): bool { /* ... */ return true; }
    public function printLabel(): void { echo "Invoice"; }
}
```

---

[Previous](./[18]-Encapsulation-and-Visibility.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[20]-Traits.md)
