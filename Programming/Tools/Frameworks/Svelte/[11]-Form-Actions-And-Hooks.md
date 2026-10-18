[Previous](./[10]-Loading-Data.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[12]-Rendering-Modes-And-Adapters.md)

*SvelteKit*

# Lesson 11 - Form Actions And Hooks

## 11.1 Form actions

A **form action** is a server function that handles a `<form>` submission. It lives in `+page.server.js` and receives the posted data.

```js
// src/routes/login/+page.server.js
import { fail, redirect } from '@sveltejs/kit';

export const actions = {
  default: async ({ request, cookies }) => {
    const data = await request.formData();
    const email = data.get('email');
    const password = data.get('password');

    if (!email || !password) {
      return fail(400, { email, missing: true });
    }

    // ... check the password, create a session ...
    cookies.set('session', 'abc123', { path: '/' });

    redirect(303, '/dashboard');
  }
};
```

```svelte
<!-- src/routes/login/+page.svelte -->
<script>
  let { form } = $props();
</script>

<form method="POST">
  <input name="email" type="email" value={form?.email ?? ''} />
  <input name="password" type="password" />

  {#if form?.missing}
    <p class="error">Please fill in every field.</p>
  {/if}

  <button>Log in</button>
</form>
```

Key points:

- `fail(status, data)` sends data back **without** leaving the page, so you can show validation errors. The returned data appears as the `form` prop.
- `redirect(303, '/path')` sends the user elsewhere after success.
- Use `method="POST"` on the form. No JavaScript is needed for it to work.

**Named actions** let one page handle several forms. The action is chosen with `?/name`:

```js
export const actions = {
  login: async ({ request }) => { /* ... */ },
  register: async ({ request }) => { /* ... */ }
};
```

```svelte
<form method="POST" action="?/login">...</form>
<form method="POST" action="?/register">...</form>
```

Do not mix a `default` action with named ones on the same page.

---

## 11.2 Progressive enhancement

Plain HTML forms reload the page when submitted. Add the `enhance` action to make the form submit with `fetch` instead, keeping scroll position, focus, and page state, while still falling back to normal behavior if JavaScript fails.

```svelte
<script>
  import { enhance } from '$app/forms';

  let { form } = $props();
  let loading = $state(false);
</script>

<form
  method="POST"
  use:enhance={() => {
    loading = true;

    return async ({ update }) => {
      await update(); // apply the result: update `form`, run invalidation
      loading = false;
    };
  }}
>
  <input name="email" type="email" />
  <button disabled={loading}>{loading ? 'Sending...' : 'Send'}</button>
</form>
```

- `use:enhance` with no argument already gives you the smooth default behavior.
- The callback runs **before** submit. It can read `formData`, call `cancel()`, and return a function that runs **after** the response arrives, receiving the `result` and an `update` helper.
- This idea is called **progressive enhancement**: the site works for everyone, and gets nicer when JavaScript is available.

---

## 11.3 Hooks and handle

**Hooks** are app-wide functions that SvelteKit calls at specific moments. They live in `src/hooks.server.js` (server) and `src/hooks.client.js` (browser).

The most important is **`handle`**, which runs for every request on the server. Use it for sessions, authentication, and custom headers.

```js
// src/hooks.server.js
export async function handle({ event, resolve }) {
  const session = event.cookies.get('session');

  // Make user info available to every load function and action
  event.locals.user = session ? await getUserFromSession(session) : null;

  const response = await resolve(event);
  response.headers.set('x-powered-by', 'SvelteKit');
  return response;
}

async function getUserFromSession(session) {
  // Look up the session in your database here
  return { id: 1, name: 'Ada' };
}
```

Anything saved on `event.locals` is available in `+page.server.js`, `+layout.server.js`, and `+server.js` files.

Other hooks:

| Hook | Where | Purpose |
|---|---|---|
| `handle` | server | Runs on every request |
| `handleFetch` | server | Modify `fetch` calls made inside load functions |
| `handleError` | server and client | Runs on unexpected errors (see 11.4) |
| `reroute` | universal (`hooks.js`) | Change which route a URL maps to |

To combine several `handle` functions, use `sequence` from `@sveltejs/kit/hooks`.

---

## 11.4 Error handling

SvelteKit separates errors into two types.

**Expected errors** are ones you anticipate, like "not found". Create them with `error()`. The message is shown to the user.

```js
import { error } from '@sveltejs/kit';

export async function load({ params }) {
  const post = await getPost(params.slug);
  if (!post) error(404, 'Post not found');
  return { post };
}
```

**Unexpected errors** are bugs, such as a failed database connection. The user sees a generic "Internal Error" message, so internal details are never leaked. Handle them in `handleError`, for logging and reporting:

```js
// src/hooks.server.js
export function handleError({ error, event }) {
  console.error('Unexpected error at', event.url.pathname, error);

  return {
    message: 'Something went wrong. Please try again.'
  };
}
```

**Error pages.** Add a `+error.svelte` file to customize what the user sees. SvelteKit shows the closest one up the route tree.

```svelte
<!-- src/routes/+error.svelte -->
<script>
  import { page } from '$app/state';
</script>

<h1>{page.status}</h1>
<p>{page.error?.message}</p>
<a href="/">Go home</a>
```

Notes:

- If even the root layout fails, SvelteKit falls back to `src/error.html`.
- In TypeScript projects, describe the shape of error objects by editing `App.Error` in `src/app.d.ts`.
- `error()` and `redirect()` do not need `throw`. Just call them.

[Previous](./[10]-Loading-Data.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[12]-Rendering-Modes-And-Adapters.md)