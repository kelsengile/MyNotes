[Previous](./[28]-Attributes.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[30]-PHP-Version-Highlights.md)

*Advanced Language Features*

# Lesson 29 - Reflection

## 29.1 What is the Reflection API?

Reflection lets PHP code inspect classes, methods, properties, and functions **at runtime** — their names, types, visibility, attributes, and more. It's the mechanism behind many framework features: dependency injection containers, ORMs, and attribute-based routing all rely on it.

---

## 29.2 Inspecting Classes with ReflectionClass

```php
<?php
class Product {
    public string $name = "Widget";
    private float $price = 9.99;

    public function describe(): string {
        return $this->name;
    }
}

$reflection = new \ReflectionClass(Product::class);

echo $reflection->getName();        // Product
var_dump($reflection->isAbstract()); // false
```

---

## 29.3 Inspecting Methods and Properties

```php
<?php
$reflection = new \ReflectionClass(Product::class);

foreach ($reflection->getMethods() as $method) {
    echo $method->getName() . " "; // describe
}

foreach ($reflection->getProperties() as $property) {
    echo $property->getName() . " "; // name price
}
```

Reflection can even access private members for inspection purposes, by explicitly marking them accessible:

```php
<?php
$property = $reflection->getProperty('price');
$property->setAccessible(true);

$product = new Product();
echo $property->getValue($product); // 9.99
```

---

## 29.4 Practical Use Cases

- **Dependency injection containers** use reflection to inspect a class's constructor and automatically supply the objects it needs.
- **ORMs** use it to map database columns to private object properties.
- **Testing tools** use it to call or inspect private methods without changing their visibility.
- **Attribute readers**, as shown in the previous lesson, use `getAttributes()` to retrieve metadata attached to a class.

Reflection is a powerful tool for library and framework authors, but it's rarely needed in typical application code — most of the time, plain object-oriented code is clearer and faster.

---

[Previous](./[28]-Attributes.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[30]-PHP-Version-Highlights.md)
