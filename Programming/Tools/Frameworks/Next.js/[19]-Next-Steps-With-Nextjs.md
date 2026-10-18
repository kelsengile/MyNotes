[Previous](./[18]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md)

*Quality And Delivery*

# Lesson 19 - Next Steps With Next.js

## 19.1 The Pages Router and migration

Before the App Router, Next.js used the **Pages Router** (the `pages/` directory). It is still supported, and you will meet it in many existing projects and tutorials.

| | **Pages Router** | **App Router** |
|---|---|---|
| Directory | `pages/` | `app/` |
| Components | Client-rendered with SSR | Server Components by default |
| Data fetching | `getStaticProps`, `getServerSideProps` | `async` components with `fetch` or database calls |
| Layouts | `_app.tsx`, `_document.tsx` | Nested `layout.tsx` files |
| API | `pages/api/*` | `route.ts` handlers |

Pages Router example:

```tsx
// pages/posts.tsx
export async function getServerSideProps() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();
  return { props: { posts } };
}

export default function Posts({ posts }: { posts: { id: number; title: string }[] }) {
  return <ul>{posts.map((p) => <li key={p.id}>{p.title}</li>)}</ul>;
}
```

You can **migrate gradually**, because both routers can exist in the same project. Steps:

1. Upgrade to a recent Next.js and React version.
2. Create the `app/` directory with a root `layout.tsx`.
3. Move routes one at a time from `pages/` to `app/`. If the same URL exists in both, the build errors, so remove the old file when you move it.
4. Replace `getStaticProps` and `getServerSideProps` with direct data fetching in Server Components.
5. Replace `next/router` with `next/navigation`.
6. Replace `pages/api` with Route Handlers.
7. Replace `next/head` with the Metadata API.

The official migration guide covers edge cases in detail.

---

## 19.2 Internationalization

**Internationalization (i18n)** lets your site serve multiple languages. The App Router does not include a built-in translation system, but it provides the building blocks: routing, middleware, and Server Components.

A common pattern is a **locale segment** in the URL:

```
app/
└── [lang]/
    ├── layout.tsx
    ├── page.tsx          →  /en, /fr, /es
    └── about/page.tsx    →  /en/about, /fr/about
```

```tsx
// app/[lang]/page.tsx
import { getDictionary } from "./dictionaries";

export default async function Home({
  params,
}: {
  params: Promise<{ lang: "en" | "fr" }>;
}) {
  const { lang } = await params;
  const t = await getDictionary(lang);
  return <h1>{t.welcome}</h1>;
}
```

```ts
// app/[lang]/dictionaries.ts
const dictionaries = {
  en: () => import("./dictionaries/en.json").then((m) => m.default),
  fr: () => import("./dictionaries/fr.json").then((m) => m.default),
};

export const getDictionary = async (locale: "en" | "fr") => dictionaries[locale]();
```

Use **middleware** to detect the user's preferred language (from the `Accept-Language` header or a cookie) and redirect `/` to `/en` or `/fr`.

Things to handle:

- `<html lang={lang}>` in the root layout
- Localized metadata and `hreflang` alternates for SEO
- Date, number, and currency formatting with `Intl`
- Right-to-left languages (set `dir="rtl"`)

Libraries such as **next-intl** wrap these patterns with a friendlier API.

---

## 19.3 Monorepos

A **monorepo** keeps several related projects (apps and shared packages) in one repository. It is useful when you have, for example, a marketing site, an admin dashboard, and shared UI components.

A typical layout:

```
my-monorepo/
├── apps/
│   ├── web/          # Next.js app
│   └── admin/        # another Next.js app
├── packages/
│   ├── ui/           # shared React components
│   ├── config/       # shared ESLint / TypeScript config
│   └── db/           # shared database client
├── package.json
└── turbo.json
```

Common tools:

| Tool | Role |
|---|---|
| **Turborepo** | Fast builds with caching and task pipelines (made by Vercel) |
| **Nx** | Full-featured build system |
| **npm / pnpm / Yarn workspaces** | Link local packages together |

To use shared packages that are written in TypeScript (not pre-compiled), tell Next.js to transpile them:

```ts
// apps/web/next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  transpilePackages: ["@repo/ui"],
};

export default nextConfig;
```

Then import them like any dependency:

```tsx
import { Button } from "@repo/ui";
```

Benefits: code reuse, consistent tooling, and atomic changes across projects. The trade-offs are more setup and the need for good build caching as the repository grows. Deploy platforms like Vercel can build a specific app by setting the project's root directory.

---

## 19.4 Project ideas

The best way to learn is to build. Ideas from easiest to hardest:

**Beginner**

- **Personal portfolio**: static pages, `next/image`, metadata, and a contact page.
- **Markdown blog**: read `.md` files from disk, use `generateStaticParams`, and add a sitemap.
- **Weather dashboard**: fetch a public API on the server, with search through `searchParams`.

**Intermediate**

- **To-do or notes app**: a database with Prisma or Drizzle, Server Actions, optimistic UI, and validation.
- **Recipe or movie browser**: dynamic routes, pagination, loading states, and error pages.
- **URL shortener**: Route Handlers, redirects, and database storage.

**Advanced**

- **E-commerce store**: product catalog with ISR, cart, authentication, payments (such as Stripe webhooks), and on-demand revalidation.
- **SaaS dashboard**: role-based access, middleware protection, charts, and a settings area.
- **Multilingual site**: locale routing, translated content, and SEO alternates.
- **Real-time collaboration tool**: authentication, a database, and live updates.

Tips for growing further:

- Deploy every project so you practice the full cycle.
- Write at least a few tests (Lesson 16).
- Read the official Next.js documentation and release notes regularly, because the framework evolves quickly.
- Study open-source Next.js projects to see how others structure real applications.
- Revisit performance (Lesson 17) with real measurements.

Congratulations on completing the Next.js Topic!

[Previous](./[18]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md)