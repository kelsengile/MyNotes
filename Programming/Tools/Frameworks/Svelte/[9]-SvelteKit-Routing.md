[Previous](./[8]-Transitions-And-Animations.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[10]-Loading-Data.md)

*SvelteKit*

# Lesson 9 - SvelteKit Routing

**SvelteKit** is the official application framework for Svelte. It adds routing, data loading, server rendering, and deployment tools. Its router is **file-based**: the folders inside `src/routes` become the URLs of your site.

## 9.1 File-based routes

```
src/routes/
├── +page.svelte              →  /
├── about/
│   └── +page.svelte          →  /about
└── blog/
    ├── +page.svelte          →  /blog
    └── first-post/
        └── +page.svelte      →  /blog/first-post
```

A folder becomes a URL segment, and a file named `+page.svelte` is the page shown at that URL. Files starting with `+` are special SvelteKit files:

| File | Purpose |
|---|---|
| `+page.svelte` | The page component |
| `+page.js` | Loads data for the page (runs on server and browser) |
| `+page.server.js` | Loads data and handles forms (server only) |
| `+layout.svelte` | Wrapper shared by pages |
| `+layout.js` / `+layout.server.js` | Loads data for a layout |
| `+server.js` | API endpoint |
| `+error.svelte` | Error page for this route |

A basic page:

```svelte
<!-- src/routes/about/+page.svelte -->
<svelte:head>
  <title>About</title>
</svelte:head>

<h1>About us</h1>
<a href="/">Back home</a>
```

Navigation:

- Normal `<a href="/about">` links work. SvelteKit intercepts them and navigates without a full page reload.
- To navigate from code, use `goto`:

```svelte
<script>
  import { goto } from '$app/navigation';
</script>

<button onclick={() => goto('/about')}>Go to About</button>
```

- Anything in `src/lib` can be imported using the `$lib` alias, for example `import Button from '$lib/Button.svelte'`.

---

## 9.2 Layouts

A `+layout.svelte` wraps every page in its folder and in all folders below it. It is a good place for navigation bars and footers. The wrapped page is passed in as `children`.

```svelte
<!-- src/routes/+layout.svelte -->
<script>
  let { children } = $props();
</script>

<nav>
  <a href="/">Home</a>
  <a href="/blog">Blog</a>
  <a href="/about">About</a>
</nav>

<main>
  {@render children()}
</main>

<footer>My site</footer>
```

Layouts **nest**. A layout at `src/routes/blog/+layout.svelte` is rendered inside the root layout, and only wraps the blog pages:

```
src/routes/
├── +layout.svelte        (wraps everything)
└── blog/
    ├── +layout.svelte    (wraps only /blog and below)
    └── +page.svelte
```

Layouts stay mounted while you move between the pages inside them, so their state is kept. A layout can also load data (Lesson 10) that is shared with all the pages beneath it.

---

## 9.3 Dynamic and optional parameters

Put a segment name in **square brackets** to match any value in that position.

| Folder | Matches | Example URL |
|---|---|---|
| `blog/[slug]` | One required segment | `/blog/hello-world` |
| `files/[[lang]]` | One optional segment | `/files` or `/files/en` |
| `docs/[...path]` | Any number of segments | `/docs/a/b/c` |

Read the value in a `load` function through `params`:

```js
// src/routes/blog/[slug]/+page.js
export function load({ params }) {
  return { slug: params.slug };
}
```

```svelte
<!-- src/routes/blog/[slug]/+page.svelte -->
<script>
  let { data } = $props();
</script>

<h1>Post: {data.slug}</h1>
```

You can also read it directly in a component using the `page` object:

```svelte
<script>
  import { page } from '$app/state';
</script>

<p>Current slug: {page.params.slug}</p>
```

Older projects use the `$page` store from `$app/stores`, which works the same way.

**Param matchers** restrict what a segment may be. Create `src/params/integer.js`:

```js
export function match(value) {
  return /^\d+$/.test(value);
}
```

Then name the folder `[id=integer]`. Only numeric values match, and anything else falls through to other routes or a 404.

---

## 9.4 Route groups

A folder name in **parentheses** is a **route group**. It organizes files and shares a layout but does **not** appear in the URL.

```
src/routes/
├── (marketing)/
│   ├── +layout.svelte        marketing header and footer
│   ├── +page.svelte          →  /
│   └── pricing/
│       └── +page.svelte      →  /pricing
└── (app)/
    ├── +layout.svelte        app sidebar
    └── dashboard/
        └── +page.svelte      →  /dashboard
```

The words `(marketing)` and `(app)` never show up in a URL. Groups are useful when:

- Different areas of the site need different layouts (a public site and a logged-in app).
- You want to organize a large routes folder without changing URLs.
- You want to protect a whole group of pages with one layout or load function.

If a group needs to **escape** a parent layout, you can name a file `+page@.svelte` (or `+layout@.svelte`) to reset to the root layout. See the SvelteKit docs on "layout resets" for details.

[Previous](./[8]-Transitions-And-Animations.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[10]-Loading-Data.md)