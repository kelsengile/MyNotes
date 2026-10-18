[Previous](./[3]-Controllers-And-Requests.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[5]-Database-And-Migrations.md)

*Basics*

# Lesson 4 - Blade Templates

## 4.1 Blade syntax

Blade is Laravel's templating engine. Files end in `.blade.php` and live in `resources/views`. Blade is compiled into plain PHP and cached, so it is fast.

**Printing data:**

```blade
<h1>{{ $title }}</h1>              {{-- escaped, safe against XSS --}}
<div>{!! $trustedHtml !!}</div>    {{-- NOT escaped, use only with trusted content --}}
{{-- This is a Blade comment and does not appear in the HTML --}}
```

`{{ }}` automatically runs `htmlspecialchars`, which protects you from cross-site scripting. Only use `{!! !!}` for content you fully control.

**Conditionals and loops:**

```blade
@if ($count > 10)
    Many
@elseif ($count > 0)
    Some
@else
    None
@endif

@foreach ($posts as $post)
    <li>{{ $loop->iteration }}. {{ $post->title }}</li>
@endforeach

@forelse ($posts as $post)
    <li>{{ $post->title }}</li>
@empty
    <li>No posts yet.</li>
@endforelse
```

Inside loops the `$loop` variable gives `index`, `iteration`, `first`, `last`, `count`, and more.

**Forms:**

```blade
<form method="POST" action="{{ route('posts.update', $post) }}">
    @csrf
    @method('PUT')
    ...
</form>
```

- `@csrf` adds the hidden token that protects against cross-site request forgery.
- `@method('PUT')` spoofs PUT/PATCH/DELETE because HTML forms only support GET and POST.

---

## 4.2 Layouts and components

Blade offers two ways to share page structure. Modern Laravel favors **components**.

**Layout component** — create `resources/views/components/layout.blade.php`:

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <title>{{ $title ?? 'My App' }}</title>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
<body>
    <nav>...</nav>
    <main>{{ $slot }}</main>
</body>
</html>
```

Use it in a page:

```blade
<x-layout title="Posts">
    <h1>All posts</h1>
</x-layout>
```

Everything between the tags goes into `$slot`. Attributes such as `title="Posts"` become variables.

**Anonymous components** are just Blade files in `resources/views/components`. `components/alert.blade.php` becomes `<x-alert>`:

```blade
@props(['type' => 'info'])

<div class="alert alert-{{ $type }}">
    {{ $slot }}
</div>
```

```blade
<x-alert type="danger">Something went wrong.</x-alert>
```

**Class-based components** add PHP logic:

```bash
php artisan make:component PostCard
```

This creates `app/View/Components/PostCard.php` and `resources/views/components/post-card.blade.php`, used as `<x-post-card :post="$post" />`. A colon before an attribute passes a PHP expression instead of a string.

**Older approach: template inheritance**

```blade
{{-- layouts/app.blade.php --}}
<title>@yield('title')</title>
<body>@yield('content')</body>

{{-- page --}}
@extends('layouts.app')
@section('title', 'Home')
@section('content')
    <p>Welcome</p>
@endsection
```

You will meet this in older projects. Both approaches work.

---

## 4.3 Directives

Directives are `@`-prefixed shortcuts for common PHP patterns.

| Directive | Purpose |
|---|---|
| `@auth` / `@guest` | Show content to logged-in users / guests |
| `@can('update', $post)` | Show content if authorized (see Lesson 11) |
| `@isset($x)` / `@empty($x)` | Check whether a variable is set / empty |
| `@error('email')` | Show a validation error for a field |
| `@include('partials.footer')` | Include another view |
| `@php ... @endphp` | Run raw PHP (use rarely) |
| `@push('scripts')` / `@stack('scripts')` | Add content to a named stack defined in a layout |
| `@vite([...])` | Load assets processed by Vite |
| `@class([...])` | Build conditional CSS classes |

Examples:

```blade
@auth
    <p>Welcome back, {{ auth()->user()->name }}</p>
@endauth

<input name="email" value="{{ old('email') }}">
@error('email')
    <span class="error">{{ $message }}</span>
@enderror

<div @class(['card', 'card-active' => $isActive])></div>
```

You can also write your own directives with `Blade::directive()` in a service provider, but components usually cover those needs better.

---

## 4.4 Passing data to views

From a route or controller:

```php
return view('posts.show', ['post' => $post]);

return view('posts.show', compact('post', 'comments')); // same names as variables

return view('posts.show')->with('post', $post);
```

Dot notation maps to folders: `view('posts.show')` loads `resources/views/posts/show.blade.php`.

**Sharing data with every view:**

```php
use Illuminate\Support\Facades\View;

// In AppServiceProvider::boot()
View::share('siteName', 'My Blog');
```

**View composers** supply data to specific views only when they render:

```php
View::composer('partials.sidebar', function ($view) {
    $view->with('popularPosts', Post::popular()->take(5)->get());
});
```

Check whether a view exists with `view()->exists('posts.show')`.

Keep logic out of templates. Blade should present data. Queries and calculations belong in controllers, models, or components.

[Previous](./[3]-Controllers-And-Requests.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[5]-Database-And-Migrations.md)