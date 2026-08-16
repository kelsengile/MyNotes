[Previous](./[20]-Traits.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[22]-Static-Members-and-Constants.md)

*Object-Oriented Programming*

# Lesson 21 - Magic Methods

## 21.1 What Are Magic Methods?

Magic methods are special methods PHP calls automatically in response to certain actions on an object, always prefixed with a double underscore (`__`). You've already met two of them: `__construct` and `__destruct`.

---

## 21.2 __toString

`__toString` lets an object define how it should appear when used as a string, such as inside `echo`:

```php
<?php
class Money {
    public function __construct(private float $amount) {}

    public function __toString(): string {
        return sprintf('$%.2f', $this->amount);
    }
}

$price = new Money(9.5);
echo $price; // $9.50
```

---

## 21.3 __get and __set

These intercept access to properties that are inaccessible or don't exist, useful for lazy-loading or validation:

```php
<?php
class Config {
    private array $data = [];

    public function __get($name) {
        return $this->data[$name] ?? null;
    }

    public function __set($name, $value) {
        $this->data[$name] = $value;
    }
}

$config = new Config();
$config->theme = "dark"; // triggers __set
echo $config->theme;     // triggers __get, outputs "dark"
```

---

## 21.4 __call and __callStatic

These intercept calls to methods that don't exist, commonly used to build fluent APIs or proxies:

```php
<?php
class Router {
    public function __call($name, $arguments) {
        echo "Called '$name' with: " . implode(', ', $arguments);
    }
}

$router = new Router();
$router->get('/home'); // Called 'get' with: /home
```

`__callStatic` works the same way but for calls to undefined **static** methods. Magic methods are powerful but can make code harder to follow — use them intentionally, not as a default habit.

---

[Previous](./[20]-Traits.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[22]-Static-Members-and-Constants.md)
