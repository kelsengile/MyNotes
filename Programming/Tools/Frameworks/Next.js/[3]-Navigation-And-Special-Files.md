[Previous](./[2]-Routing-With-The-App-Router.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[4]-Server-And-Client-Components.md)

*Basics*

# Lesson 3 - Navigation And Special Files

## 3.1 The Link component and navigation hooks

Use `<Link>` from `next/link` instead of a plain `<a>` for internal navigation. It performs client-side navigation (no full page reload) and **prefetches** linked routes when they appear in the viewport.

```tsx
import Link from "next/link";

export default function Nav() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/blog/hello-world">Blog post</Link>
      <Link href="/search?q=next" prefetch={false}>Search</Link>
    </nav>
  );
}
```

Navigation hooks come from `next/navigation` and only work in **Client Components** (files starting with `"use client"`, see Lesson 4):

```tsx
"use client";

import { usePathname, useRouter, useSearchParams } from "next/navigation";

export default function Controls() {
  const pathname = usePathname();          // "/blog/hello"
  const searchParams = useSearchParams();  // URLSearchParams
  const router = useRouter();

  return (
    <button onClick={() => router.push("/dashboard")}>
      Go to dashboard (from {pathname}, q={searchParams.get("q")})
    </button>
  );
}
```

Useful `router` methods: `push`, `replace`, `back`, `forward`, `refresh`, and `prefetch`.

A common use of `usePathname` is highlighting the active link in a navigation bar.

---

## 3.2 loading.js and streaming UI

Add a `loading.tsx` file to a route segment and Next.js automatically wraps the page in a React `<Suspense>` boundary, showing your loading UI instantly while the page's data loads.

```tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return <p>Loading dashboard...</p>;
}
```

```
app/dashboard/
├── layout.tsx     # Shows immediately
├── loading.tsx    # Shown while page.tsx is loading
└── page.tsx       # Async page that fetches data
```

This enables **streaming**: the server sends the layout and fallback first, then streams the finished page when it is ready. The user sees something immediately instead of a blank screen. The layout stays interactive during loading.

For finer control, wrap individual components in `<Suspense>` yourself (covered in Lesson 5).

---

## 3.3 error.js and not-found.js

**`error.tsx`** catches unexpected errors in a segment and its children, so the rest of the app keeps working. It must be a Client Component.

```tsx
// app/dashboard/error.tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

`reset()` tries to re-render the segment. To catch errors in the root layout itself, use `app/global-error.tsx`, which must define its own `<html>` and `<body>`.

**`not-found.tsx`** renders when a URL matches no route, or when you call `notFound()` manually:

```tsx
// app/blog/[slug]/page.tsx
import { notFound } from "next/navigation";

export default async function Post({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;
  const post = await getPost(slug); // your data function
  if (!post) notFound();
  return <h1>{post.title}</h1>;
}
```

```tsx
// app/not-found.tsx
export default function NotFound() {
  return <h1>404 - Page not found</h1>;
}
```

`notFound()` also sends a 404 status code, which matters for SEO.

---

## 3.4 Redirects

There are several ways to redirect, depending on where the logic lives.

**In a Server Component or Server Action** use `redirect()`:

```tsx
import { redirect } from "next/navigation";

export default async function Profile() {
  const user = await getUser();
  if (!user) redirect("/login");
  return <h1>Welcome, {user.name}</h1>;
}
```

`redirect()` sends a temporary redirect (307, or 303 in Server Actions). Use `permanentRedirect()` for a permanent one (308).

**In `next.config.ts`** for fixed rules:

```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  async redirects() {
    return [
      {
        source: "/old-blog/:slug",
        destination: "/blog/:slug",
        permanent: true,
      },
    ];
  },
};

export default nextConfig;
```

**In middleware** for conditional logic based on the request (see Lesson 12).

**In a Client Component** use `router.push()` or `router.replace()` from `useRouter`.

Note: `redirect()` works by throwing a special error internally, so do not call it inside a `try/catch` block unless you re-throw it.

[Previous](./[2]-Routing-With-The-App-Router.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[4]-Server-And-Client-Components.md)