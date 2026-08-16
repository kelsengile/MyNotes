[Previous](./[21]-Magic-Methods.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[23]-Enums.md)

*Object-Oriented Programming*

# Lesson 22 - Static Members And Constants

## 22.1 Static Properties and Methods

`static` members belong to the class itself rather than to any single instance, and are shared across every instance:

```php
<?php
class Counter {
    public static int $total = 0;

    public static function increment(): void {
        self::$total++;
    }
}

Counter::increment();
Counter::increment();
echo Counter::$total; // 2
```

Static members are accessed with `::` rather than `->`, and without needing to create an object first.

---

## 22.2 Class Constants

Constants hold values that never change and are defined with `const`:

```php
<?php
class Circle {
    public const PI = 3.14159;

    public function __construct(private float $radius) {}

    public function area(): float {
        return self::PI * $this->radius ** 2;
    }
}

echo Circle::PI; // 3.14159
```

---

## 22.3 The self vs static Keyword

`self::` refers to the exact class the code is written in; `static::` refers to whichever class was actually called at runtime — this matters when inheritance is involved:

```php
<?php
class Base {
    public static function create(): static {
        return new static();
    }
}

class Child extends Base {}

echo get_class(Child::create()); // Child — "static" resolves to the calling class
```

---

## 22.4 The final Keyword

`final` prevents a class from being extended, or a method from being overridden — useful when you want to guarantee behavior can't be changed by a subclass:

```php
<?php
final class Configuration {
    // this class cannot be extended
}

class Payment {
    final public function process(): void {
        // this method cannot be overridden
    }
}
```

---

[Previous](./[21]-Magic-Methods.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[23]-Enums.md)
