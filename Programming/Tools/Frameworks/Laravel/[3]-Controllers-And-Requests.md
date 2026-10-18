[Previous](./[2]-Routing.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[4]-Blade-Templates.md)

*Basics*

# Lesson 3 - Controllers And Requests

## 3.1 Creating controllers

Controllers group related request-handling logic into classes inside `app/Http/Controllers`.

```bash
php artisan make:controller PostController
```

```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::latest()->get();

        return view('posts.index', ['posts' => $posts]);
    }

    public function show(Post $post)
    {
        return view('posts.show', ['post' => $post]);
    }
}
```

Register the routes:

```php
Route::get('/posts', [PostController::class, 'index'])->name('posts.index');
Route::get('/posts/{post}', [PostController::class, 'show'])->name('posts.show');
```

A **single-action (invokable) controller** has one `__invoke` method:

```bash
php artisan make:controller ShowDashboardController --invokable
```

```php
Route::get('/dashboard', ShowDashboardController::class);
```

Controllers can receive dependencies through constructor or method type-hints, and Laravel injects them (see Lesson 13).

---

## 3.2 Resource controllers

Most apps repeat the same seven CRUD actions. A resource controller scaffolds them:

```bash
php artisan make:controller PostController --resource --model=Post
```

```php
Route::resource('posts', PostController::class);
```

That one line registers:

| Verb | URL | Method | Route name |
|---|---|---|---|
| GET | `/posts` | `index` | `posts.index` |
| GET | `/posts/create` | `create` | `posts.create` |
| POST | `/posts` | `store` | `posts.store` |
| GET | `/posts/{post}` | `show` | `posts.show` |
| GET | `/posts/{post}/edit` | `edit` | `posts.edit` |
| PUT/PATCH | `/posts/{post}` | `update` | `posts.update` |
| DELETE | `/posts/{post}` | `destroy` | `posts.destroy` |

Limit the actions when you do not need all of them:

```php
Route::resource('posts', PostController::class)->only(['index', 'show']);
Route::resource('posts', PostController::class)->except(['destroy']);
```

For APIs, use `apiResource`, which omits `create` and `edit` because there are no HTML forms:

```php
Route::apiResource('posts', PostController::class);
```

---

## 3.3 The Request object

`Illuminate\Http\Request` holds everything about the incoming HTTP request. Type-hint it in a controller method:

```php
use Illuminate\Http\Request;

public function store(Request $request)
{
    $title = $request->input('title');          // from query string or body
    $title = $request->title;                   // dynamic property shortcut
    $page  = $request->query('page', 1);        // query string only, with default
    $all   = $request->all();                   // everything
    $some  = $request->only(['title', 'body']); // just these keys
    $rest  = $request->except(['_token']);      // everything but these

    if ($request->has('title')) { /* key is present */ }
    if ($request->filled('title')) { /* present and not empty */ }

    $request->method();        // GET, POST ...
    $request->path();          // posts/create
    $request->url();           // full URL without query string
    $request->ip();            // client IP
    $request->header('Accept');
    $request->user();          // the authenticated user or null
}
```

Files:

```php
if ($request->hasFile('avatar')) {
    $file = $request->file('avatar');
}
```

Route parameters can be mixed with the request. List the request first, then the route parameters:

```php
public function update(Request $request, Post $post) { /* ... */ }
```

Never trust raw input. Validate it first (Lesson 9) and use `$request->validated()` or `only()`.

---

## 3.4 Responses and redirects

A controller can return many kinds of values and Laravel converts them to HTTP responses:

```php
return 'plain text';                          // text/html
return ['name' => 'Ada'];                     // arrays become JSON
return view('posts.index', compact('posts')); // rendered Blade view
return response()->json(['ok' => true], 201); // JSON with status code
return response('Created', 201)->header('X-Custom', 'yes');
return response()->download($pathToFile);
```

**Redirects:**

```php
return redirect('/posts');                              // to a URL
return redirect()->route('posts.show', $post);          // to a named route
return back();                                          // to the previous page
return redirect()->route('posts.index')
    ->with('status', 'Post created!');                  // flash data for one request
return back()->withInput();                             // keep old form input
```

Flash data is available in the next request's session, for example in Blade:

```blade
@if (session('status'))
    <div class="alert">{{ session('status') }}</div>
@endif
```

**Aborting with an error status:**

```php
abort(404);
abort(403, 'You are not allowed here.');
abort_if(! $user->isAdmin(), 403);
```

Follow the **Post/Redirect/Get** pattern: after a successful form submission, redirect instead of rendering a view, so refreshing the browser does not resubmit the form.

[Previous](./[2]-Routing.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[4]-Blade-Templates.md)