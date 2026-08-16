[Previous](./[16]-Constructors-and-Destructors.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[18]-Encapsulation-and-Visibility.md)

*Object-Oriented Programming*

# Lesson 17 - Inheritance And Polymorphism

## 17.1 Extending a Class

A class can inherit properties and methods from another using `extends`, avoiding duplicate code between related classes:

```php
<?php
class Animal {
    public function __construct(public string $name) {}

    public function speak(): string {
        return "$this->name makes a sound.";
    }
}

class Dog extends Animal {}

$dog = new Dog("Rex");
echo $dog->speak(); // Rex makes a sound.
```

---

## 17.2 Overriding Methods

A child class can redefine a method it inherits to change its behavior:

```php
<?php
class Dog extends Animal {
    public function speak(): string {
        return "$this->name barks.";
    }
}

$dog = new Dog("Rex");
echo $dog->speak(); // Rex barks.
```

---

## 17.3 The parent Keyword

Use `parent::` to call the parent class's version of a method or constructor from within the child:

```php
<?php
class Dog extends Animal {
    public function __construct(string $name, public string $breed) {
        parent::__construct($name);
    }

    public function speak(): string {
        return parent::speak() . " Specifically, a bark.";
    }
}
```

---

## 17.4 Polymorphism in Practice

Polymorphism means objects of different classes can be treated through a shared parent type, while each still runs its own version of a method:

```php
<?php
class Cat extends Animal {
    public function speak(): string {
        return "$this->name meows.";
    }
}

$animals = [new Dog("Rex"), new Cat("Milo")];

foreach ($animals as $animal) {
    echo $animal->speak(); // each calls its own overridden version
}
```

This lets you write code that works with the general `Animal` type without needing to know which specific subclass it's handling.

---

[Previous](./[16]-Constructors-and-Destructors.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[18]-Encapsulation-and-Visibility.md)
