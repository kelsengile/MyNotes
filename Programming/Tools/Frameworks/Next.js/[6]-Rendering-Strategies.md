[Previous](./[5]-Data-Fetching.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[7]-Caching-And-Revalidation.md)

*Data And Rendering*

# Lesson 6 - Rendering Strategies

## 6.1 Static rendering

With **static rendering**, a route is rendered **at build time** (when you run `next build`). The resulting HTML is stored and served to every visitor, often from a CDN. It is the fastest option.

A route is static when it uses no request-specific information. This page is static:

```tsx
// app/about/page.tsx
export default function About() {
  return <h1>About us</h1>;
}
```

For dynamic routes, tell Next.js which pages to build ahead of time with `generateStaticParams`:

```tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await getAllPosts();
  return posts.map((post) => ({ slug: post.slug }));
}

export default async function Post({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;
  const post = await getPost(slug);
  return <article>{post.title}</article>;
}
```

Best for: marketing pages, blogs, documentation, anything that is the same for everyone.

---

## 6.2 Dynamic rendering

With **dynamic rendering**, a route is rendered **on the server for each request**. Next.js switches to it automatically when it detects a **dynamic API**:

- `cookies()` and `headers()`
- `searchParams` prop
- `connection()`
- Uncached data fetches (for example `fetch(url, { cache: "no-store" })`)

```tsx
import { cookies } from "next/headers";

export default async function Dashboard() {
  const cookieStore = await cookies();
  const theme = cookieStore.get("theme")?.value ?? "light";
  return <p>Your theme: {theme}</p>;
}
```

You can also force a mode with a route segment config:

```tsx
export const dynamic = "force-dynamic"; // always dynamic
// other values: "auto" (default), "force-static", "error"
```

Best for: personalized pages, dashboards, real-time data, pages that depend on the incoming request.

The `next build` output lists each route with a symbol showing whether it is static (`○`), dynamic (`ƒ`), or statically generated with params (`●`).

---

## 6.3 Incremental static regeneration

**Incremental Static Regeneration (ISR)** gives you static speed with periodically fresh content. A page is served from cache, and after a time window passes, it is regenerated in the background.

```tsx
// app/products/page.tsx
export const revalidate = 60; // seconds

export default async function Products() {
  const products = await getProducts();
  return <ul>{products.map((p) => <li key={p.id}>{p.name}</li>)}</ul>;
}
```

Flow:

1. First visit after build: serves the static page.
2. After 60 seconds, the next visitor still gets the cached page, and a regeneration starts in the background.
3. When regeneration finishes, later visitors get the new page.

This is called **stale-while-revalidate**. You can also revalidate on demand (see Lesson 7). For dynamic routes not listed in `generateStaticParams`, Next.js can generate them on the first request and then cache them (controlled by `dynamicParams`).

Best for: product catalogs, news listings, content that changes occasionally but not per user.

---

## 6.4 Partial prerendering

Most real pages mix static and dynamic content: a static header and product info, plus a dynamic cart or personalized banner. **Partial Prerendering (PPR)** lets one route have both. The static shell is served instantly, and dynamic parts stream in afterward inside `<Suspense>` boundaries.

```tsx
import { Suspense } from "react";

export default function ProductPage() {
  return (
    <main>
      <ProductInfo />                       {/* static shell */}
      <Suspense fallback={<p>Loading cart...</p>}>
        <Cart />                            {/* dynamic, streamed */}
      </Suspense>
    </main>
  );
}
```

The key idea: **Suspense boundaries mark the line between static and dynamic**. Everything outside them is prerendered; everything inside that uses dynamic APIs is rendered at request time.

PPR has evolved across Next.js releases (it began as an experimental flag and later became part of the newer caching model). The exact configuration option depends on your version, so check the official Next.js documentation for the version you are using.

| Strategy | When rendered | Speed | Freshness |
|---|---|---|---|
| Static | Build time | Fastest | Only at build |
| ISR | Build + background refresh | Very fast | Periodic |
| Dynamic | Every request | Slower | Always fresh |
| Partial prerendering | Static shell + streamed dynamic parts | Fast | Mixed |

[Previous](./[5]-Data-Fetching.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[7]-Caching-And-Revalidation.md)