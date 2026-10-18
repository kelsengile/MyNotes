[Previous](./[16]-Building-APIs-With-Laravel.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[18]-The-Artisan-Console.md)

*APIs And Frontend*

# Lesson 17 - Frontend Stacks

## 17.1 Vite and assets

**Vite** is the build tool Laravel uses to process CSS and JavaScript. It gives instant hot reloading in development and optimized bundles for production.

Important files:

- `vite.config.js` — configures Vite and the Laravel plugin, listing the entry files.
- `resources/js/app.js` and `resources/css/app.css` — your source files.
- `package.json` — frontend dependencies and scripts.

```js
// vite.config.js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

Load the assets in your layout with the `@vite` directive:

```blade
<head>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
```

Commands:

```bash
npm install        # install frontend dependencies
npm run dev        # development server with hot reload
npm run build      # production build into public/build
```

Common errors: seeing "Vite manifest not found" means you have not run `npm run dev` or `npm run build`. New Laravel projects usually include Tailwind CSS already configured. Import third-party libraries in `app.js` with normal `import` statements after installing them with npm.

---

## 17.2 Livewire

**Livewire** lets you build dynamic interfaces using PHP and Blade, with very little JavaScript. A Livewire component is a PHP class tied to a Blade view. The page updates by sending small requests to the server in the background.

```bash
composer require livewire/livewire
php artisan make:livewire Counter
```

```php
<?php

namespace App\Livewire;

use Livewire\Component;

class Counter extends Component
{
    public int $count = 0;

    public function increment(): void
    {
        $this->count++;
    }

    public function render()
    {
        return view('livewire.counter');
    }
}
```

```blade
{{-- resources/views/livewire/counter.blade.php --}}
<div>
    <button wire:click="increment">+</button>
    <span>{{ $count }}</span>
</div>
```

Use it in any Blade page:

```blade
<livewire:counter />
```

Key directives:

| Directive | Purpose |
|---|---|
| `wire:click="method"` | Call a PHP method on click |
| `wire:model="property"` | Two-way bind an input to a property |
| `wire:model.live` | Update the property as the user types |
| `wire:submit="save"` | Handle a form submit |
| `wire:loading` | Show content while a request is running |

Livewire is great when your team knows PHP well and you want interactive features (live search, forms, tables, modals) without a separate JavaScript application. Properties are validated and secured on the server, but remember that public properties are visible to the browser, so do not store secrets in them.

---

## 17.3 Inertia with Vue or React

**Inertia.js** lets you build a single-page application (SPA) using **Vue** or **React** while keeping Laravel routing and controllers. There is no separate API: controllers return Inertia responses, and the page is rendered by a JavaScript component.

Controller:

```php
use Inertia\Inertia;

public function index()
{
    return Inertia::render('Posts/Index', [
        'posts' => Post::latest()->get(['id', 'title']),
    ]);
}
```

Vue page (`resources/js/Pages/Posts/Index.vue`):

```vue
<script setup>
import { Link } from '@inertiajs/vue3';
defineProps({ posts: Array });
</script>

<template>
    <h1>Posts</h1>
    <ul>
        <li v-for="post in posts" :key="post.id">
            <Link :href="`/posts/${post.id}`">{{ post.title }}</Link>
        </li>
    </ul>
</template>
```

The React version is nearly identical: the page is a component that receives `posts` as a prop.

How it works: the first page load is a normal request. After that, clicking an Inertia `Link` makes an XHR request and Laravel replies with JSON (component name plus props). Inertia swaps the component without a full page reload.

Benefits: you keep server-side routing, authentication, validation, and authorization, and also get a smooth SPA feel. Validation errors arrive as a shared `errors` prop. You can scaffold it through Breeze or the official starter kits (Lesson 10).

---

## 17.4 Choosing a stack

There is no single best choice. Use these questions to decide:

| Stack | Best when | Trade-offs |
|---|---|---|
| **Blade (+ Alpine.js)** | Mostly content pages and simple forms; you want the least complexity | Full page reloads unless you add JavaScript yourself |
| **Livewire** | Team is PHP-focused; you want interactivity with little JavaScript | Each interaction talks to the server; heavy client-side effects are harder |
| **Inertia + Vue/React** | You want a modern SPA with rich interactivity; team knows Vue or React | Two languages to maintain; needs a Node build step |
| **API + separate front end** | Mobile app or multiple clients share one backend; separate front-end team | Most work: you handle auth tokens, CORS, and two deployments |

Guidelines:

- Starting out? Begin with Blade, then add Livewire when you need interactivity.
- If you already love Vue or React, Inertia gives you that without building an API.
- If you need to serve a mobile app as well, build a proper API (Lesson 16) and consume it from the web front end if needed.
- Think about hiring: choose what your team can maintain.
- You can mix approaches. For example a Blade site with a few Livewire components or Alpine.js widgets.

Whatever you choose, the Laravel backend concepts (routing, Eloquent, validation, authentication, queues) stay the same.

[Previous](./[16]-Building-APIs-With-Laravel.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[18]-The-Artisan-Console.md)