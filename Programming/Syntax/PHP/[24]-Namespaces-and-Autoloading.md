[Previous](./[23]-Enums.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[25]-Closures-and-Anonymous-Functions.md)

*Object-Oriented Programming*

# Lesson 24 - Namespaces And Autoloading

## 24.1 What Are Namespaces?

Namespaces group related classes, functions, and constants under a common prefix, preventing naming collisions between your code and third-party libraries that might define a class with the same name.

---

## 24.2 Declaring and Using Namespaces

A namespace declaration must be the first statement in a file:

```php
<?php
// File: src/Billing/Invoice.php
namespace App\Billing;

class Invoice {
    public function total(): float {
        return 100.0;
    }
}
```

To use that class elsewhere, reference its full path or import it with `use`:

```php
<?php
require 'src/Billing/Invoice.php';

$invoice = new \App\Billing\Invoice();
```

---

## 24.3 PSR-4 Autoloading

Manually `require`-ing every file becomes unmanageable. **PSR-4** is a standard that maps namespaces to folder structures, so Composer can autoload classes automatically. Configure it in `composer.json`:

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    }
}
```

After running `composer dump-autoload`, any class under `App\` (e.g. `App\Billing\Invoice`) is automatically found in `src/Billing/Invoice.php` — no manual `require` needed, as long as `vendor/autoload.php` is included once.

---

## 24.4 use Statements and Aliasing

The `use` keyword imports a namespaced class so you can reference it by its short name, optionally under an alias:

```php
<?php
namespace App\Reports;

use App\Billing\Invoice;
use App\Billing\Invoice as BillingInvoice; // alias, useful to avoid name clashes

$invoice = new Invoice();
```

---

[Previous](./[23]-Enums.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[25]-Closures-and-Anonymous-Functions.md)
