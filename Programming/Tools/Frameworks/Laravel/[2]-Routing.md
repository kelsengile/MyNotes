[Previous](./[1]-What-Is-Laravel.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[3]-Controllers-And-Requests.md)

*Basics*

# Lesson 2 - Routing

## 2.1 Defining routes

Routes connect a URL and HTTP method to some code. Web routes live in `routes/web.php`.

```php
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/hello', fn () => 'Hello, world!');

Route::post('/contact', [ContactController::class, 'store']);
```

Available HTTP verb methods: `get`, `post`, `put`, `patch`, `delete`, `options`, plus `match(['get', 'post'], ...)` and `any(...)`.

Shortcuts:

```php
Route::view('/about', 'about', ['company' => 'Acme']); // returns a view directly
Route::redirect('/old-page', '/new-page', 301);        // permanent redirect
```

A route action can be a **closure** (good for quick tests) or a **controller method** (better for real apps, see Lesson 3).

Useful command:

```bash
php artisan route:list
```

It prints every registered route with its method, URL, name, and action.

---

## 2.2 Route parameters

Wrap a URL segment in braces to capture it:

```php
Route::get('/users/{id}', function (string $id) {
    return "User $id";
});
```

Make a parameter optional with `?` and give it a default value:

```php
Route::get('/greet/{name?}', function (string $name = 'Guest') {
    return "Hello, $name";
});
```

Constrain parameters with `where` helpers or regular expressions:

```php
Route::get('/posts/{id}', fn (string $id) => $id)->whereNumber('id');
Route::get('/tags/{slug}', fn (string $slug) => $slug)->whereAlpha('slug');
Route::get('/code/{code}', fn (string $code) => $code)->where('code', '[A-Z]{3}');
```

If a request does not satisfy the constraint, Laravel returns a 404.

---

## 2.3 Named routes and groups

**Named routes** let you refer to a route by name instead of a hard-coded URL. If you later change the URL, every link still works.

```php
Route::get('/dashboard', [DashboardController::class, 'index'])->name('dashboard');

// Generating URLs
$url = route('dashboard');
return redirect()->route('dashboard');
```

With parameters:

```php
Route::get('/users/{user}', [UserController::class, 'show'])->name('users.show');

route('users.show', ['user' => 5]); // /users/5
```

**Groups** share settings between many routes:

```php
Route::prefix('admin')
    ->name('admin.')
    ->middleware('auth')
    ->group(function () {
        Route::get('/users', [AdminUserController::class, 'index'])->name('users'); // admin.users
        Route::get('/reports', [ReportController::class, 'index'])->name('reports'); // admin.reports
    });
```

- `prefix('admin')` adds `/admin` to each URL.
- `name('admin.')` prepends `admin.` to each route name.
- `middleware('auth')` applies middleware to every route in the group.

You can also group by controller:

```php
Route::controller(OrderController::class)->group(function () {
    Route::get('/orders', 'index');
    Route::post('/orders', 'store');
});
```

---

## 2.4 Route model binding

Instead of receiving an ID and loading the model yourself, let Laravel do it.

**Implicit binding** — the parameter name matches the type-hinted variable name:

```php
use App\Models\Post;

Route::get('/posts/{post}', function (Post $post) {
    return $post->title;
});
```

Laravel runs the equivalent of `Post::findOrFail($id)`. If no record exists it returns a 404 automatically.

Bind by a column other than `id`:

```php
Route::get('/posts/{post:slug}', function (Post $post) {
    return $post->title;
});
```

Or set it globally on the model:

```php
public function getRouteKeyName(): string
{
    return 'slug';
}
```

**Explicit binding** is registered manually, usually in `AppServiceProvider::boot()`:

```php
Route::bind('post', function (string $value) {
    return Post::where('slug', $value)->firstOrFail();
});
```

You can also bind related models with scoping so that `/users/{user}/posts/{post}` only finds posts that belong to that user:

```php
Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
    return $post;
})->scopeBindings();
```

[Previous](./[1]-What-Is-Laravel.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[3]-Controllers-And-Requests.md)