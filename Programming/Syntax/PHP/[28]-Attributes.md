[Previous](./[27]-Type-Declarations-and-Strict-Typing.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[29]-Reflection.md)

*Advanced Language Features*

# Lesson 28 - Attributes

## 28.1 What Are Attributes?

Attributes (PHP 8+) let you attach structured metadata to classes, methods, properties, and functions, using a syntax built into the language itself rather than parsing special comments. Frameworks use them heavily for things like routing and validation.

---

## 28.2 Attribute Syntax

An attribute is written above the target using `#[...]`:

```php
<?php
#[Route('/users', method: 'GET')]
class UserController {
    #[Deprecated]
    public function oldMethod(): void {}
}
```

An attribute is just a regular class. To define your own, add the `#[Attribute]` attribute to a class:

```php
<?php
#[\Attribute]
class Route {
    public function __construct(
        public string $path,
        public string $method = 'GET',
    ) {}
}
```

---

## 28.3 Built-in Attributes

PHP ships with a few built-in attributes, the most common being `#[Deprecated]` and `#[Override]` (PHP 8.3+), which signal intent to both developers and static analysis tools:

```php
<?php
class Report {
    #[Override]
    public function render(): string {
        return "Report content";
    }
}
```

---

## 28.4 Reading Attributes with Reflection (Preview)

Attributes are inert on their own — something has to read them at runtime to act on them. That "something" is the **Reflection API**, which we cover in full in the next lesson:

```php
<?php
$reflection = new \ReflectionClass(UserController::class);
$attributes = $reflection->getAttributes(Route::class);

foreach ($attributes as $attribute) {
    $route = $attribute->newInstance();
    echo $route->path; // /users
}
```

---

[Previous](./[27]-Type-Declarations-and-Strict-Typing.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[29]-Reflection.md)
