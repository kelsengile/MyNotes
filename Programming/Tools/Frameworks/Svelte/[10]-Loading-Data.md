[Previous](./[9]-SvelteKit-Routing.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[11]-Form-Actions-And-Hooks.md)

*SvelteKit*

# Lesson 10 - Loading Data

## 10.1 load functions

A page usually needs data before it shows. In SvelteKit you fetch it in a **`load` function**, which runs before the page renders. The value you return becomes the page's `data` prop.

```js
// src/routes/blog/[slug]/+page.js
import { error } from '@sveltejs/kit';

export async function load({ fetch, params }) {
  const res = await fetch(`/api/posts/${params.slug}`);

  if (!res.ok) {
    error(404, 'Post not found');
  }

  const post = await res.json();
  return { post };
}
```

```svelte
<!-- src/routes/blog/[slug]/+page.svelte -->
<script>
  let { data } = $props();
</script>

<h1>{data.post.title}</h1>
<p>{data.post.body}</p>
```

Why use `load` instead of fetching in `onMount`?

- The data is ready **before** the page appears, so there is no loading flicker.
- The data is loaded during server rendering, so search engines see the real content.
- SvelteKit manages caching, navigation, and errors for you.

The `load` function receives an event object with useful properties: `params`, `url`, `fetch`, `parent`, `depends`, and `route`. Always use the provided `fetch` instead of the global one, as explained in 10.3.

---

## 10.2 Server vs universal loading

There are two kinds of load files:

| | Universal: `+page.js` | Server: `+page.server.js` |
|---|---|---|
| Runs on | Server during first load, then browser for later navigations | Server only |
| Can use secrets, databases, private env vars | No | Yes |
| Can return | Anything (classes, components) | Only serializable data (JSON-like, dates, maps, sets, promises) |
| Has access to | `params`, `url`, `fetch` | Also `cookies`, `locals`, `request`, `getClientAddress()` |

Use a **server** load when you read a database, use private keys, or need cookies:

```js
// src/routes/dashboard/+page.server.js
import { redirect } from '@sveltejs/kit';
import { db } from '$lib/server/db.js';

export async function load({ locals }) {
  if (!locals.user) {
    redirect(303, '/login');
  }

  const orders = await db.getOrders(locals.user.id);
  return { orders };
}
```

Use a **universal** load when you fetch from a public API, or need to return something that cannot be serialized.

Code placed in a folder named `$lib/server` (or in files named `*.server.js`) can never be imported by browser code. SvelteKit raises an error at build time if you try, which protects your secrets.

Both can be used for the same route. The server load runs first, and its result is passed to the universal load as `data`.

---

## 10.3 Fetching and invalidation

**The special `fetch`.** The `fetch` passed to `load` is enhanced:

- It works with relative URLs on the server.
- It forwards cookies and authorization headers for same-origin requests.
- During server rendering, the response is saved and reused in the browser, so the request is not repeated.

**When does `load` run again?** SvelteKit tracks what the function used. It re-runs when:

- A `params` or `url` value it read changes.
- A `fetch` URL or dependency it relied on is **invalidated**.
- `invalidateAll()` is called.

**Custom dependencies** let you refresh data on demand:

```js
// +page.js
export async function load({ fetch, depends }) {
  depends('app:todos');

  const res = await fetch('/api/todos');
  return { todos: await res.json() };
}
```

```svelte
<script>
  import { invalidate, invalidateAll } from '$app/navigation';

  let { data } = $props();

  async function refresh() {
    await invalidate('app:todos'); // re-run only loads that depend on this key
    // or: await invalidateAll();  // re-run every active load
  }
</script>

<button onclick={refresh}>Refresh</button>
```

**Parent data.** A load function can wait for a layout's data with `await parent()`. Use it sparingly, because it forces loads to run one after another instead of in parallel.

---

## 10.4 Streaming data

Sometimes part of the data is slow. Instead of making the user wait for everything, return fast data immediately and **stream** the slow data as unawaited promises. This works in `+page.server.js` files.

```js
// src/routes/product/[id]/+page.server.js
import { getProduct, getReviews } from '$lib/server/shop.js';

export async function load({ params }) {
  const product = await getProduct(params.id); // fast: we wait for it

  return {
    product,
    reviews: getReviews(params.id) // slow: NOT awaited, it streams in later
  };
}
```

```svelte
<script>
  let { data } = $props();
</script>

<h1>{data.product.name}</h1>

{#await data.reviews}
  <p>Loading reviews...</p>
{:then reviews}
  <ul>
    {#each reviews as review (review.id)}
      <li>{review.text}</li>
    {/each}
  </ul>
{:catch}
  <p>Could not load reviews.</p>
{/await}
```

The page renders at once with the product, and the reviews appear when their promise resolves.

Notes:

- Only **server** load functions can stream.
- Streaming needs JavaScript in the browser to receive the late data.
- To avoid unhandled rejections, make sure the promise is consumed by an `{#await}` block with a `{:catch}`.

[Previous](./[9]-SvelteKit-Routing.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[11]-Form-Actions-And-Hooks.md)