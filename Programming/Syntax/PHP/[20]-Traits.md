[Previous](./[19]-Interfaces-and-Abstract-Classes.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[21]-Magic-Methods.md)

*Object-Oriented Programming*

# Lesson 20 - Traits

## 20.1 What Problem Do Traits Solve?

PHP classes can only extend one parent, but sometimes unrelated classes need to share the same reusable chunk of behavior. **Traits** let you "mix in" methods to multiple classes without using inheritance.

---

## 20.2 Defining and Using a Trait

```php
<?php
trait Loggable {
    public function log(string $message): void {
        echo "[LOG] $message";
    }
}

class Order {
    use Loggable;
}

class User {
    use Loggable;
}

$order = new Order();
$order->log("Order created."); // [LOG] Order created.
```

Both `Order` and `User` now have a `log()` method, without being related through inheritance.

---

## 20.3 Conflict Resolution

If a class uses two traits that define the same method, PHP requires you to resolve the conflict explicitly with `insteadof` and `as`:

```php
<?php
trait A {
    public function hello() { echo "Hello from A"; }
}
trait B {
    public function hello() { echo "Hello from B"; }
}

class Greeter {
    use A, B {
        A::hello insteadof B;
        B::hello as helloFromB;
    }
}

$g = new Greeter();
$g->hello();       // Hello from A
$g->helloFromB();  // Hello from B
```

---

## 20.4 Traits vs Interfaces vs Abstract Classes

- **Interface** — defines *what* a class must do, no code included.
- **Abstract class** — defines shared behavior through single inheritance.
- **Trait** — shares reusable code across unrelated classes, without an inheritance relationship or a contract requirement.

Use traits sparingly — for genuinely reusable, self-contained behavior like logging or timestamping — rather than as a substitute for good class design.

---

[Previous](./[19]-Interfaces-and-Abstract-Classes.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[21]-Magic-Methods.md)
