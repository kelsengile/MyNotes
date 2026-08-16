[Previous](./[15]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[17]-Inheritance-and-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 16 - Constructors And Destructors

## 16.1 The __construct Method

A constructor runs automatically when an object is created, letting you set up its initial state:

```php
<?php
class Car {
    public string $color;
    public string $model;

    public function __construct(string $color, string $model) {
        $this->color = $color;
        $this->model = $model;
    }
}

$car = new Car("blue", "Hatchback");
```

---

## 16.2 Constructor Property Promotion

PHP 8 lets you declare and assign properties directly in the constructor signature, removing the boilerplate from the example above:

```php
<?php
class Car {
    public function __construct(
        public string $color,
        public string $model,
    ) {}
}

$car = new Car("blue", "Hatchback");
echo $car->color; // blue
```

---

## 16.3 The $this Keyword

Inside a method, `$this` refers to the specific object the method was called on, allowing each instance to keep its own data separate:

```php
<?php
class Wallet {
    public function __construct(public float $balance) {}

    public function deposit(float $amount): void {
        $this->balance += $amount;
    }
}

$a = new Wallet(100);
$b = new Wallet(50);
$a->deposit(20);

echo $a->balance; // 120
echo $b->balance; // 50 — unaffected
```

---

## 16.4 The __destruct Method

A destructor runs automatically when an object is no longer referenced or when the script ends — useful for cleanup like closing a file handle or connection:

```php
<?php
class FileLogger {
    public function __destruct() {
        echo "Logger closed.";
    }
}

$logger = new FileLogger();
unset($logger); // triggers __destruct, prints "Logger closed."
```

---

[Previous](./[15]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[17]-Inheritance-and-Polymorphism.md)
