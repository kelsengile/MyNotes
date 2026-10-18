[Previous](./[10]-Authentication.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[12]-Middleware.md)

*Application Features*

# Lesson 11 - Authorization

## 11.1 Gates

**Authentication** asks who you are. **Authorization** asks what you are allowed to do. A **gate** is a closure that decides whether a user may perform an action.

Define gates in `AppServiceProvider::boot()`:

```php
use App\Models\Post;
use App\Models\User;
use Illuminate\Support\Facades\Gate;

public function boot(): void
{
    Gate::define('update-post', function (User $user, Post $post) {
        return $user->id === $post->user_id;
    });

    Gate::define('view-admin', fn (User $user) => $user->is_admin);
}
```

The first parameter is always the current user (Laravel passes it automatically). Check gates:

```php
if (Gate::allows('update-post', $post)) { /* allowed */ }
if (Gate::denies('update-post', $post)) { /* not allowed */ }

Gate::authorize('update-post', $post);   // throws a 403 if denied

$request->user()->can('update-post', $post);
```

In Blade:

```blade
@can('update-post', $post)
    <a href="{{ route('posts.edit', $post) }}">Edit</a>
@endcan
```

You can also return a message with `Response::deny('Not yours')` (from `Illuminate\Auth\Access\Response`) for richer feedback. Gates are best for actions that are not tied to a specific model.

---

## 11.2 Policies

A **policy** is a class that organizes authorization logic for one model. Prefer policies for model-based rules.

```bash
php artisan make:policy PostPolicy --model=Post
```

```php
<?php

namespace App\Policies;

use App\Models\Post;
use App\Models\User;

class PostPolicy
{
    public function viewAny(User $user): bool
    {
        return true;
    }

    public function update(User $user, Post $post): bool
    {
        return $user->id === $post->user_id;
    }

    public function delete(User $user, Post $post): bool
    {
        return $user->id === $post->user_id || $user->is_admin;
    }
}
```

Laravel automatically discovers a `PostPolicy` for the `Post` model, so no registration is usually needed.

Use it in a controller:

```php
public function update(Request $request, Post $post)
{
    $this->authorize('update', $post); // 403 if denied
    // or: Gate::authorize('update', $post);

    $post->update($request->validated());
}
```

In Blade:

```blade
@can('delete', $post)
    <form method="POST" action="{{ route('posts.destroy', $post) }}">...</form>
@endcan
```

For methods that do not take an existing model (like `create`), pass the class name: `Gate::authorize('create', Post::class)`.

---

## 11.3 Roles and permissions

Laravel does not include roles by default, but it is easy to build simple ones.

**Simple approach: a `role` column**

```php
$table->string('role')->default('member');
```

A backed enum keeps the values safe:

```php
enum Role: string
{
    case Admin = 'admin';
    case Editor = 'editor';
    case Member = 'member';
}
```

```php
// User model
protected function casts(): array
{
    return ['role' => Role::class];
}

public function isAdmin(): bool
{
    return $this->role === Role::Admin;
}
```

Use it from gates and policies:

```php
Gate::define('manage-users', fn (User $user) => $user->isAdmin());
```

**Giving admins a master key:** a `before` callback runs before every other check. Return `true` to allow everything, or `null` to continue normally.

```php
Gate::before(function (User $user, string $ability) {
    if ($user->isAdmin()) {
        return true;
    }
});
```

**Advanced approach:** when you need many roles with many permissions that change at runtime, use the popular package **spatie/laravel-permission**:

```bash
composer require spatie/laravel-permission
```

```php
$user->assignRole('editor');
$user->givePermissionTo('edit posts');
$user->can('edit posts');
```

---

## 11.4 Protecting routes

The `can` middleware runs a gate or policy before the route executes:

```php
Route::put('/posts/{post}', [PostController::class, 'update'])
    ->middleware('can:update,post');

Route::get('/admin', AdminController::class)
    ->middleware(['auth', 'can:view-admin']);

Route::post('/posts', [PostController::class, 'store'])
    ->middleware('can:create,App\Models\Post');
```

In `can:update,post`, `post` is the route parameter (the bound model).

**Authorizing resource controllers at once** — in a controller:

```php
use Illuminate\Routing\Controllers\HasMiddleware;
use Illuminate\Routing\Controllers\Middleware;

class PostController extends Controller implements HasMiddleware
{
    public static function middleware(): array
    {
        return [new Middleware('can:update,post', only: ['edit', 'update'])];
    }
}
```

Or, simply call `$this->authorize()` inside each action.

**Handling denied requests:** Laravel returns a `403 Forbidden` response. To use a custom page, create `resources/views/errors/403.blade.php`. To return `404` instead of `403` (so users cannot discover that a resource exists), use `Response::denyAsNotFound()` in the policy.

Always authorize on the server. Hiding a button in a view with `@can` improves the interface but does not secure the route by itself.

[Previous](./[10]-Authentication.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[12]-Middleware.md)