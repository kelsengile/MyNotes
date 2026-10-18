[Previous](./[1]-What-Is-Nextjs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[3]-Navigation-And-Special-Files.md)

*Basics*

# Lesson 2 - Routing With The App Router

## 2.1 File-based routing

In the App Router, **folders define routes** and a special file named `page.tsx` makes a route publicly accessible.

```
app/
├── page.tsx              →  /
├── about/
│   └── page.tsx          →  /about
└── blog/
    ├── page.tsx          →  /blog
    └── first-post/
        └── page.tsx      →  /blog/first-post
```

A folder without a `page.tsx` is not a route by itself. This means you can safely keep components, styles, and helpers next to pages (this is called **colocation**).

Common route files:

| File | Purpose |
|---|---|
| `page.tsx` | The unique UI of a route |
| `layout.tsx` | Shared UI wrapping a segment and its children |
| `template.tsx` | Like a layout, but re-created on navigation |
| `loading.tsx` | Loading UI (see Lesson 3) |
| `error.tsx` | Error UI (see Lesson 3) |
| `route.ts` | An API endpoint (see Lesson 9) |

---

## 2.2 Layouts and templates

A **layout** wraps pages and nested layouts. It stays mounted and keeps its state when you navigate between pages that share it.

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <section>
      <nav>Dashboard menu</nav>
      {children}
    </section>
  );
}
```

Layouts nest automatically. A page at `app/dashboard/settings/page.tsx` renders inside the dashboard layout, which renders inside the root layout.

A **template** looks the same in code but behaves differently: a new instance is mounted on every navigation, so state resets and effects run again. Use templates for things like enter/exit animations or per-page logging.

```tsx
// app/dashboard/template.tsx
export default function DashboardTemplate({
  children,
}: {
  children: React.ReactNode;
}) {
  return <div>{children}</div>;
}
```

Layouts do not re-render when moving between sibling pages, and they cannot read the current URL path directly.

---

## 2.3 Dynamic routes

Wrap a folder name in square brackets to create a **dynamic segment**.

```
app/blog/[slug]/page.tsx   →  /blog/hello, /blog/my-post, ...
```

The value arrives in the `params` prop. In current versions of Next.js, `params` is a **Promise**, so you `await` it in a Server Component:

```tsx
// app/blog/[slug]/page.tsx
export default async function BlogPost({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;
  return <h1>Post: {slug}</h1>;
}
```

Other dynamic patterns:

| Folder | Matches | `params` |
|---|---|---|
| `[slug]` | `/a` | `{ slug: "a" }` |
| `[...slug]` (catch-all) | `/a/b/c` | `{ slug: ["a","b","c"] }` |
| `[[...slug]]` (optional catch-all) | `/` and `/a/b` | `{ slug?: [...] }` |

Query strings (`?q=next`) are available through the `searchParams` prop, which is also a Promise:

```tsx
export default async function Search({
  searchParams,
}: {
  searchParams: Promise<{ q?: string }>;
}) {
  const { q } = await searchParams;
  return <p>You searched for: {q}</p>;
}
```

---

## 2.4 Route groups and parallel routes

**Route groups** use parentheses, such as `(marketing)`. The folder name is **not** part of the URL. Use them to organize code or to give different sections different layouts.

```
app/
├── (marketing)/
│   ├── layout.tsx        # Marketing layout
│   ├── page.tsx          →  /
│   └── pricing/page.tsx  →  /pricing
└── (app)/
    ├── layout.tsx        # App layout
    └── dashboard/page.tsx →  /dashboard
```

**Parallel routes** use `@name` folders called **slots**. They let one layout render several pages at once, for example a dashboard with independent panels.

```
app/dashboard/
├── layout.tsx
├── page.tsx
├── @analytics/page.tsx
└── @team/page.tsx
```

```tsx
// app/dashboard/layout.tsx
export default function Layout({
  children,
  analytics,
  team,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <>
      {children}
      {analytics}
      {team}
    </>
  );
}
```

Slots are not URL segments. Each slot can have its own `loading.tsx` and `error.tsx`, so one panel can load or fail without affecting the others. A `default.tsx` file inside a slot provides a fallback when Next.js cannot recover the slot's active state, for example after a full page reload.

[Previous](./[1]-What-Is-Nextjs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[3]-Navigation-And-Special-Files.md)