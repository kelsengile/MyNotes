[Previous](./[11]-Form-Actions-And-Hooks.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[13]-TypeScript-And-Tooling.md)

*SvelteKit*

# Lesson 12 - Rendering Modes And Adapters

## 12.1 SSR, SSG, and prerendering

By default, SvelteKit uses **server-side rendering (SSR)**:

1. The server renders your page to HTML and sends it. The user sees content quickly and search engines can read it.
2. The browser downloads the JavaScript and **hydrates** the page, which attaches interactivity to the existing HTML.
3. Later navigations happen in the browser as a single-page app.

Terms:

| Term | Meaning |
|---|---|
| **SSR** | HTML is generated on the server for each request |
| **SSG / prerendering** | HTML is generated once at **build time** and served as static files |
| **CSR** | The page is rendered in the browser with JavaScript |

You control each page or layout with exported options:

```js
// +page.js or +layout.js
export const prerender = true;   // build this page into static HTML
export const ssr = true;         // render on the server (default)
export const csr = true;         // ship JavaScript to the browser (default)
```

**Prerender** pages whose content is the same for every visitor, such as marketing pages, docs, and blog posts. Do not prerender pages that depend on the current user or on cookies.

Details:

- `prerender = 'auto'` prerenders when possible but still allows the page to render dynamically for routes not found during the build.
- SvelteKit finds prerenderable pages by following links from your entry points. For dynamic routes it cannot discover, export an `entries()` function listing the parameter values.
- Setting `csr = false` makes a page work with no JavaScript at all, but disables client-side interactivity on it.

---

## 12.2 Client-only pages

Some pages must run only in the browser, for example when they rely on `window`, canvas, or a browser-only library. Turn off server rendering for them:

```js
// +page.js
export const ssr = false;
```

You can turn it off for a whole section by putting the option in a `+layout.js`.

To build a classic **single-page app (SPA)** with no server rendering at all:

```js
// src/routes/+layout.js
export const ssr = false;
```

```js
// svelte.config.js
import adapter from '@sveltejs/adapter-static';

export default {
  kit: {
    adapter: adapter({ fallback: '200.html' }) // the SPA entry page
  }
};
```

Check whether code runs in the browser with the `browser` flag:

```svelte
<script>
  import { browser } from '$app/environment';

  if (browser) {
    console.log('Running in the browser');
  }
</script>
```

SPA mode loses SSR's benefits (slower first paint, weaker search engine visibility), so use it only when your hosting or app truly requires it.

---

## 12.3 Adapters

An **adapter** turns your built SvelteKit app into the output that a particular host expects. You select one in `svelte.config.js`.

| Adapter | Use it for |
|---|---|
| `@sveltejs/adapter-auto` | Default. Detects some common platforms and picks the matching adapter |
| `@sveltejs/adapter-node` | A Node.js server (VPS, Docker, your own machine) |
| `@sveltejs/adapter-static` | Fully static sites (every page prerendered) |
| `@sveltejs/adapter-vercel` | Vercel |
| `@sveltejs/adapter-netlify` | Netlify |
| `@sveltejs/adapter-cloudflare` | Cloudflare Pages and Workers |

Install and configure one, for example the Node adapter:

```bash
npm install -D @sveltejs/adapter-node
```

```js
// svelte.config.js
import adapter from '@sveltejs/adapter-node';
import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';

export default {
  preprocess: vitePreprocess(),
  kit: {
    adapter: adapter()
  }
};
```

After `npm run build`, run the server with `node build`.

For a fully static site, use `adapter-static` and make sure every page is prerendered (for example `export const prerender = true;` in the root `+layout.js`).

---

## 12.4 API endpoints

A `+server.js` file creates an **endpoint**, which is a route that returns data instead of a page. Export a function for each HTTP method you support: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`.

```js
// src/routes/api/todos/+server.js
import { json, error } from '@sveltejs/kit';

let todos = [{ id: 1, text: 'Learn SvelteKit', done: false }];

export function GET() {
  return json(todos);
}

export async function POST({ request }) {
  const body = await request.json();

  if (!body.text) {
    error(400, 'text is required');
  }

  const todo = { id: Date.now(), text: body.text, done: false };
  todos.push(todo);

  return json(todo, { status: 201 });
}
```

Call it from anywhere, such as a load function or a button:

```js
const res = await fetch('/api/todos', {
  method: 'POST',
  headers: { 'content-type': 'application/json' },
  body: JSON.stringify({ text: 'Write lessons' })
});
```

Notes:

- Handlers receive the same event object as load functions (`request`, `params`, `url`, `cookies`, `locals`) and must return a standard `Response`. The `json()` helper builds one for you.
- Endpoints run only on the server, so they can use private environment variables and databases.
- The in-memory array above resets whenever the server restarts and is shared between all users. It is only for demonstration, so use a real database in a real app.
- A `+server.js` and a `+page.svelte` can live in the same folder. Browser page requests go to the page, and other requests to the endpoint.

[Previous](./[11]-Form-Actions-And-Hooks.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[13]-TypeScript-And-Tooling.md)