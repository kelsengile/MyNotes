[Previous](./[6]-Rendering-Strategies.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[8]-Server-Actions-And-Forms.md)

*Data And Rendering*

# Lesson 7 - Caching And Revalidation

## 7.1 The caching layers

Next.js caches at several levels to avoid repeating work. Caching defaults have changed between major versions, so always confirm behavior in the docs for your version.

| Layer | What it stores | Where | Lifetime |
|---|---|---|---|
| **Request Memoization** | Results of identical `fetch` calls during one render | Server | A single request |
| **Data Cache** | Results of `fetch` requests | Server | Persistent until revalidated |
| **Full Route Cache** | Rendered HTML and RSC payload of static routes | Server | Until revalidated or redeployed |
| **Router Cache** | RSC payload of visited and prefetched routes | Browser | Session or short time window |

How they work together:

- **Request Memoization** means you can call the same `fetch` in several components during one render and it runs once.
- The **Data Cache** survives between requests, so data is not refetched every time.
- The **Full Route Cache** stores the rendered output of static pages.
- The **Router Cache** makes back/forward navigation and revisits feel instant.

In recent versions (Next.js 15 and later), `fetch` requests are **not cached by default**; you opt in. Older versions (13/14) cached by default. Newer releases also introduce an explicit `"use cache"` directive for caching functions and components. Check the docs for your installed version.

---

## 7.2 Time-based revalidation

Time-based revalidation refreshes cached data after a number of seconds.

**Per request** using the `next.revalidate` option:

```tsx
const res = await fetch("https://api.example.com/products", {
  next: { revalidate: 3600 }, // cache for 1 hour
});
```

**Per route** using a segment config:

```tsx
// app/products/page.tsx
export const revalidate = 3600;
```

When both exist on a route, the **lowest** revalidate value wins for the whole route.

When the time expires, Next.js serves the stale data once while fetching fresh data in the background (stale-while-revalidate), then serves the new data.

Use this when it is acceptable for data to be slightly out of date, such as blog lists, product listings, or leaderboards.

---

## 7.3 On-demand revalidation

On-demand revalidation refreshes cached data **exactly when something changes**, instead of waiting for a timer. It is usually triggered from a Server Action or a Route Handler.

**By path:**

```ts
"use server";

import { revalidatePath } from "next/cache";

export async function createPost(formData: FormData) {
  await db.post.create({ data: { title: String(formData.get("title")) } });
  revalidatePath("/blog"); // refresh the /blog page
}
```

**By tag:** label data when you fetch it, then invalidate by label.

```tsx
// Fetch with a tag
const res = await fetch("https://api.example.com/posts", {
  next: { tags: ["posts"] },
});
```

```ts
"use server";

import { revalidateTag } from "next/cache";

export async function publishPost() {
  // ...save to database
  revalidateTag("posts"); // every fetch tagged "posts" is refreshed
}
```

Tags are powerful because one action can refresh many pages that share the same data. Signature details for `revalidateTag` have changed in newer versions, so check the docs for your version.

You can also trigger revalidation from a webhook, for example when a headless CMS publishes new content, by calling these functions inside a Route Handler.

---

## 7.4 Opting out of caching

Sometimes you always need fresh data. Options include:

**Per fetch:**

```tsx
const res = await fetch("https://api.example.com/live-price", {
  cache: "no-store",
});
```

**Per route:**

```tsx
export const dynamic = "force-dynamic";
// or
export const revalidate = 0;
```

**Using dynamic APIs.** Reading `cookies()`, `headers()`, or `searchParams` makes a route dynamic. You can also call `connection()` to explicitly wait for an incoming request:

```tsx
import { connection } from "next/server";

export default async function Page() {
  await connection(); // opt this render into dynamic behavior
  const now = new Date().toISOString();
  return <p>Rendered at {now}</p>;
}
```

**For non-fetch data** (such as direct database queries), caching works differently, since the Data Cache only applies to `fetch`. In recent versions you wrap such functions with `"use cache"` or `unstable_cache` if you want caching, and leave them unwrapped for fresh data.

On the browser side, `router.refresh()` clears the Router Cache for the current route and re-fetches it from the server.

[Previous](./[6]-Rendering-Strategies.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[8]-Server-Actions-And-Forms.md)