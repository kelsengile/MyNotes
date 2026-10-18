[Previous](./[11]-Authorization.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[13]-Service-Container-And-Providers.md)

*Application Features*

# Lesson 12 - Middleware

## 12.1 How middleware works

**Middleware** is code that sits between the incoming request and your application (and again on the way back out). It can inspect, change, or reject a request before it reaches the controller.

```
Request → [Middleware A] → [Middleware B] → Controller
Response ← [Middleware A] ← [Middleware B] ←
```

Typical uses:

- Checking that a user is logged in
- Verifying CSRF tokens
- Limiting how often a client can call a route
- Setting a locale or logging requests
- Adding headers to responses

Each middleware receives the request and a `$next` callback. It either passes the request along or returns its own response (for example, a redirect), which stops the chain.

Attach middleware to routes:

```php
Route::get('/dashboard', DashboardController::class)->middleware('auth');
Route::get('/billing', BillingController::class)->middleware(['auth', 'verified']);

Route::middleware(['auth'])->group(function () {
    // all routes here require login
});
```

---

## 12.2 Built-in middleware

| Alias | Purpose |
|---|---|
| `auth` | Redirects guests to the login page |
| `guest` | Redirects logged-in users away (for login/register pages) |
| `verified` | Requires a verified email address |
| `auth.basic` | HTTP Basic authentication |
| `can:ability,model` | Authorization check |
| `throttle:60,1` | Rate limiting |
| `signed` | Requires a valid signed URL |
| `password.confirm` | Requires the password again for sensitive actions |

**Middleware groups.** Laravel applies the `web` group (sessions, cookies, CSRF protection, route binding) to routes in `routes/web.php`, and the `api` group to API routes. In Laravel 11 and newer you adjust them in `bootstrap/app.php`:

```php
use Illuminate\Foundation\Configuration\Middleware;

->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [\App\Http\Middleware\LogVisits::class]);
    $middleware->api(prepend: [\App\Http\Middleware\ForceJson::class]);
    $middleware->append(\App\Http\Middleware\SomeGlobalMiddleware::class); // every request
    $middleware->alias(['admin' => \App\Http\Middleware\EnsureUserIsAdmin::class]);
})
```

- `append` / `prepend` add middleware to the end or start of a group.
- `alias` gives a short name you can use in routes (`->middleware('admin')`).
- Global middleware runs on every request.

---

## 12.3 Custom middleware

Generate one:

```bash
php artisan make:middleware EnsureUserIsAdmin
```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureUserIsAdmin
{
    public function handle(Request $request, Closure $next): Response
    {
        if (! $request->user()?->is_admin) {
            abort(403, 'Admins only.');
        }

        return $next($request);
    }
}
```

Register an alias in `bootstrap/app.php` (see 12.2) and use it:

```php
Route::get('/admin', AdminController::class)->middleware(['auth', 'admin']);
```

**Before vs. after.** Code before `$next($request)` runs before the controller. To act on the response, capture it first:

```php
public function handle(Request $request, Closure $next): Response
{
    // Before the controller
    $response = $next($request);
    // After the controller
    $response->headers->set('X-Frame-Options', 'SAMEORIGIN');

    return $response;
}
```

**Parameters.** Pass values after a colon:

```php
Route::get('/reports', ReportController::class)->middleware('role:editor,admin');

public function handle(Request $request, Closure $next, string ...$roles): Response
{
    if (! in_array($request->user()->role->value, $roles)) {
        abort(403);
    }
    return $next($request);
}
```

**Terminable middleware** can define `terminate($request, $response)` to run work after the response has been sent to the browser.

---

## 12.4 Rate limiting

Rate limiting stops abuse by capping how many requests a client can make in a time window.

Quick form: `throttle:max,minutes`

```php
Route::post('/login', LoginController::class)->middleware('throttle:5,1'); // 5 per minute
```

Named limiters are defined in `AppServiceProvider::boot()`:

```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});

RateLimiter::for('uploads', function (Request $request) {
    return $request->user()?->is_premium
        ? Limit::none()
        : Limit::perMinute(10)->by($request->user()->id);
});
```

Use them by name:

```php
Route::middleware('throttle:uploads')->post('/upload', UploadController::class);
```

- `by(...)` decides who is counted (the user ID, or the IP address for guests).
- When the limit is exceeded Laravel returns a `429 Too Many Requests` response with a `Retry-After` header.
- Rate limits use the cache, so make sure you have a working cache driver in production (database or Redis).

[Previous](./[11]-Authorization.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[13]-Service-Container-And-Providers.md)