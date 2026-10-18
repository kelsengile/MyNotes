[Previous](./[20]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md)

*Quality And Delivery*

# Lesson 21 - Next Steps With Laravel

## 21.1 Laravel Octane

**Octane** boosts performance by keeping your application loaded in memory between requests, instead of booting the framework for every request as traditional PHP-FPM does. It runs on a high-performance server such as **FrankenPHP**, **Swoole**, or **RoadRunner**.

```bash
composer require laravel/octane
php artisan octane:install
php artisan octane:start --workers=4
```

Why it is faster: framework booting happens once per worker, and many requests are then served from that warm application.

Things to watch for, because the app stays in memory:

- **Static properties and singletons persist** across requests. Do not store per-user data in static variables or singletons that live on.
- **Avoid injecting the request, or config that changes, into singletons** at construction time. Prefer `scoped` bindings (Lesson 13), which reset per request.
- **Memory leaks** accumulate. Octane can recycle workers with `--max-requests`.
- **Code changes** need a reload: `php artisan octane:reload` (or `--watch` in development).

Octane is an optimization, not a requirement. Most apps run perfectly well without it. Reach for it when you have measured a real performance need, and first check simpler wins such as caching, fixing N+1 queries (Lesson 7), and using queues (Lesson 14).

---

## 21.2 Packages and ecosystem

Laravel has a large ecosystem of official and community packages. Install them with Composer.

**First-party packages:**

| Package | Purpose |
|---|---|
| **Sanctum** | API tokens and SPA authentication |
| **Socialite** | Login with Google, GitHub, and other OAuth providers |
| **Horizon** | Dashboard and monitoring for Redis queues |
| **Telescope** | Debug assistant: requests, queries, jobs, mail, exceptions |
| **Pulse** | Real-time application performance monitoring |
| **Scout** | Full-text search (Meilisearch, Algolia, and others) |
| **Cashier** | Subscription billing with Stripe or Paddle |
| **Reverb** | First-party WebSocket server for real-time features |
| **Pennant** | Feature flags |
| **Pint** | Opinionated PHP code style fixer |
| **Sail** | Docker development environment |

**Popular community packages:**

- **spatie/laravel-permission** — roles and permissions
- **spatie/laravel-medialibrary** — file and image management
- **barryvdh/laravel-debugbar** — debug bar for local development
- **Larastan** — static analysis for catching bugs without running code
- **Filament** — admin panel builder
- **Livewire** and **Inertia** — frontend stacks (Lesson 17)

Choosing packages wisely:

- Check that it is maintained (recent releases, active issues) and supports your Laravel and PHP versions.
- Read the source or documentation to understand what it does before depending on it.
- Prefer a package over custom code for complex, security-sensitive problems (payments, authentication). Prefer simple custom code for small needs, to avoid unnecessary dependencies.
- Keep dependencies updated with `composer update` and review security advisories with `composer audit`.

Official documentation at laravel.com/docs is the best reference, and Laracasts, Laravel News, and the Laravel community on forums and Discord are good places to keep learning.

---

## 21.3 Clean architecture

As an application grows, organizing code well matters more than any single feature. Practical habits that fit Laravel:

**Keep controllers thin.** A controller should receive the request, call something that does the work, and return a response. Move business logic out.

**Use action or service classes** for business operations:

```php
// app/Actions/PlaceOrder.php
class PlaceOrder
{
    public function __construct(private PaymentGateway $gateway) {}

    public function handle(User $user, array $items): Order
    {
        return DB::transaction(function () use ($user, $items) {
            $order = $user->orders()->create(['status' => 'pending']);
            $order->items()->createMany($items);
            $this->gateway->charge($order->total());
            OrderPlaced::dispatch($order);

            return $order;
        });
    }
}
```

```php
public function store(StoreOrderRequest $request, PlaceOrder $placeOrder)
{
    $order = $placeOrder->handle($request->user(), $request->validated('items'));

    return redirect()->route('orders.show', $order);
}
```

**Principles to follow:**

- **Single responsibility:** each class does one job.
- **Depend on interfaces** where you may swap implementations (gateways, storage), and bind them in the container.
- **Form requests** for validation and **policies** for authorization, so those concerns stay out of controllers.
- **Events and jobs** for side effects such as emails and notifications.
- **API resources** (or view models) so internal database structure is not exposed.
- **Use database transactions** (`DB::transaction`) when several writes must succeed or fail together.
- **Write tests** around important behavior (Lesson 19), so you can refactor safely.

A warning against over-engineering: do not add repositories, DTOs, and layers "just in case". Eloquent is already an abstraction. Add structure when a real problem (duplication, hard-to-test logic, messy controllers) shows up, not before.

---

## 21.4 Project ideas

Practice turns knowledge into skill. Pick projects that exercise the lessons in this Topic, starting small:

**Beginner**

- **Personal blog:** posts, categories, tags, comments (routing, Blade, Eloquent relationships, validation).
- **To-do list:** user accounts, tasks, due dates, filters (authentication, authorization with policies).
- **URL shortener:** generate short codes, redirect, count clicks (routing, models, middleware).

**Intermediate**

- **Bookstore REST API:** books, authors, reviews, Sanctum tokens, API resources, versioning, and automated tests.
- **Job board:** companies and listings, search and filters, file upload for CVs, email notifications on application.
- **Expense tracker:** categories, monthly reports with charts, scheduled summary emails, CSV export through a queued job.

**Advanced**

- **Multi-tenant SaaS starter:** teams, roles and permissions, subscription billing with Cashier, queues, and deployment with a CI pipeline.
- **Real-time chat or notifications:** Reverb WebSockets with Livewire or Inertia.
- **Learning platform:** courses, lessons, progress tracking, quizzes, admin panel with Filament.

Ways to get the most out of each project:

1. Write down the features first, then design your database tables and relationships.
2. Build it in small steps and commit often with Git.
3. Write tests for the important behavior.
4. Deploy it (Lesson 20) so you learn the real-world workflow.
5. Read the source of the framework and good open-source Laravel projects.
6. Return later to refactor using the ideas from 21.3.

Congratulations on finishing the Laravel Topic!

[Previous](./[20]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md)