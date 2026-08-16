[Previous](./[14]-Array-Functions.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[16]-Constructors-and-Destructors.md)

*Object-Oriented Programming*

# Lesson 15 - Classes And Objects

## 15.1 What is Object-Oriented Programming?

Object-oriented programming (OOP) organizes code around **objects** — bundles of related data (properties) and behavior (methods) — instead of loose functions and variables. It helps model real-world things and keeps large codebases organized. The next ten lessons build up PHP's full OOP feature set.

---

## 15.2 Defining a Class

A class is a blueprint for creating objects:

```php
<?php
class Car {
    public string $color;
    public string $model;

    public function describe(): string {
        return "A {$this->color} {$this->model}";
    }
}
```

---

## 15.3 Creating Objects (Instances)

Use `new` to create an object — an **instance** — from a class:

```php
<?php
$car = new Car();
$car->color = "red";
$car->model = "Sedan";

echo $car->describe(); // A red Sedan
```

Each instance has its own independent copy of the class's properties.

---

## 15.4 Properties and Methods

**Properties** are variables that belong to an object; **methods** are functions that belong to it:

```php
<?php
class Counter {
    public int $count = 0;

    public function increment(): void {
        $this->count++;
    }
}

$c = new Counter();
$c->increment();
$c->increment();
echo $c->count; // 2
```

`$this` refers to the current object inside a method — we'll cover it more thoroughly in the next lesson.

---

[Previous](./[14]-Array-Functions.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[16]-Constructors-and-Destructors.md)
