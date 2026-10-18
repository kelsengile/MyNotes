[Previous](./[12]-Middleware.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[14]-Queues-Events-And-Scheduling.md)

*Application Features*

# Lesson 13 - Service Container And Providers

## 13.1 Dependency injection

A **dependency** is an object that a class needs to do its job. **Dependency injection** means a class receives its dependencies from the outside instead of creating them itself.

Without injection (hard to test and change):

```php
class OrderController
{
    public function store()
    {
        $gateway = new StripeGateway('secret-key'); // tightly coupled
    }
}
```

With injection:

```php
class OrderController
{
    public function __construct(private PaymentGateway $gateway) {}

    public function store()
    {
        $this->gateway->charge(1000);
    }
}
```

The **service container** is Laravel's tool for building objects and supplying their dependencies. If a class has no special setup, Laravel **auto-resolves** it by reading the type-hints in the constructor, with no configuration needed.

Injection works in:

- Controller constructors and controller methods
- Middleware, jobs, event listeners, form requests
- Anywhere you call `app(SomeClass::class)` or `resolve(SomeClass::class)`

```php
public function show(Request $request, PostRepository $posts, Post $post)
{
    // $request, $posts, and $post are all provided automatically
}
```

---

## 13.2 Binding services

When Laravel cannot guess how to build something, for example an **interface** or an object that needs special arguments, you **bind** it in the container.

```php
use App\Contracts\PaymentGateway;
use App\Services\StripeGateway;

// In a service provider's register() method
$this->app->bind(PaymentGateway::class, function ($app) {
    return new StripeGateway(config('services.stripe.secret'));
});
```

Binding types:

| Method | Behavior |
|---|---|
| `bind` | A new instance every time it is resolved |
| `singleton` | Created once, then the same instance is reused for the app's lifetime |
| `scoped` | Like singleton, but reset for each request or job (useful with Octane and queues) |
| `instance` | Registers an object you already built |

```php
$this->app->singleton(PaymentGateway::class, StripeGateway::class);
```

Binding an interface to a class lets you swap implementations (for example a fake gateway in tests) without touching any code that depends on the interface.

**Contextual binding** gives different implementations to different classes:

```php
$this->app->when(PhotoController::class)
    ->needs(Filesystem::class)
    ->give(fn () => Storage::disk('photos'));
```

Resolving manually:

```php
$gateway = app(PaymentGateway::class);
$gateway = resolve(PaymentGateway::class);
```

Prefer constructor injection over manual resolving where possible.

---

## 13.3 Service providers

**Service providers** are the central place where the framework and your own code are bootstrapped. Every Laravel feature (database, queue, mail, and so on) is registered by a provider.

A provider has two methods:

- `register()` — only bind things into the container. Do not use other services here, because they may not be ready yet.
- `boot()` — runs after all providers are registered. Safe to use other services, define gates, register view composers, set up rate limiters, and so on.

```bash
php artisan make:provider PaymentServiceProvider
```

```php
<?php

namespace App\Providers;

use App\Contracts\PaymentGateway;
use App\Services\StripeGateway;
use Illuminate\Support\ServiceProvider;

class PaymentServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->singleton(PaymentGateway::class, StripeGateway::class);
    }

    public function boot(): void
    {
        // e.g. publish config, register macros, define gates
    }
}
```

**Registering providers.** In Laravel 11 and newer, providers are listed in `bootstrap/providers.php`:

```php
return [
    App\Providers\AppServiceProvider::class,
    App\Providers\PaymentServiceProvider::class,
];
```

`make:provider` adds new providers there automatically. In older versions, providers were listed in `config/app.php`. Most small apps only ever need `AppServiceProvider`.

---

## 13.4 Facades explained

A **facade** is a static-looking interface to a service in the container. When you write:

```php
use Illuminate\Support\Facades\Cache;

Cache::get('key');
```

it is not a real static method. Behind the scenes the `Cache` facade asks the container for the cache service and calls `get` on that object.

Common facades: `Route`, `DB`, `Auth`, `Cache`, `Storage`, `Mail`, `Log`, `Gate`, `Http`, `Schema`, `Session`, `Validator`.

Why use them: they are short and readable, and they are easily testable because you can replace them with fakes or mocks:

```php
Cache::shouldReceive('get')->with('key')->andReturn('value');

Mail::fake();
Storage::fake('public');
```

Compare the same call using injection:

```php
use Illuminate\Contracts\Cache\Repository as Cache;

public function __construct(private Cache $cache) {}

$this->cache->get('key');
```

Both are fine. Pick the one that suits your team.

Related helpers are global functions that do the same job: `cache()`, `session()`, `config()`, `auth()`, `view()`, `redirect()`, `app()`, `logger()`.

**Real-time facades:** prefixing a class namespace with `Facades\` makes any class behave like a facade:

```php
use Facades\App\Services\Metrics;

Metrics::record('signup');
```

Create custom facades only when they truly help. Over-using them can hide your class dependencies.

[Previous](./[12]-Middleware.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[14]-Queues-Events-And-Scheduling.md)