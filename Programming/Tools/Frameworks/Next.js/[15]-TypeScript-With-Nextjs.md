[Previous](./[14]-Authentication-And-Authorization.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[16]-Testing-Nextjs-Applications.md)

*Quality And Delivery*

# Lesson 15 - TypeScript With Next.js

## 15.1 Typed routes and params

Next.js supports TypeScript out of the box. A `tsconfig.json` is created for you, and files ending in `.ts` or `.tsx` work immediately.

Type dynamic route params. In recent versions `params` and `searchParams` are Promises:

```tsx
// app/blog/[slug]/page.tsx
type Props = {
  params: Promise<{ slug: string }>;
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>;
};

export default async function Page({ params, searchParams }: Props) {
  const { slug } = await params;
  const { page } = await searchParams;
  return <h1>{slug} (page {page ?? "1"})</h1>;
}
```

Catch-all routes use arrays:

```tsx
// app/docs/[...path]/page.tsx
type Props = { params: Promise<{ path: string[] }> };
```

**Typed routes** is an optional feature that makes `<Link href>` check that the route actually exists, catching typos at compile time:

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  typedRoutes: true,
};

export default nextConfig;
```

The option's location has moved between versions, so check the docs for yours. The `@/*` import alias in `tsconfig.json` lets you write `import { db } from "@/lib/db"` instead of long relative paths.

---

## 15.2 Typing props and actions

**Component props:**

```tsx
type ButtonProps = {
  label: string;
  variant?: "primary" | "secondary";
  onClick?: () => void;
};

export default function Button({ label, variant = "primary", onClick }: ButtonProps) {
  return <button className={variant} onClick={onClick}>{label}</button>;
}
```

**Children and layouts:**

```tsx
export default function Layout({ children }: { children: React.ReactNode }) {
  return <main>{children}</main>;
}
```

**Server Actions:**

```ts
"use server";

type ActionResult =
  | { success: true; id: number }
  | { success: false; error: string };

export async function createPost(formData: FormData): Promise<ActionResult> {
  const title = formData.get("title");
  if (typeof title !== "string" || title.length === 0) {
    return { success: false, error: "Title is required" };
  }
  const post = await db.post.create({ data: { title } });
  return { success: true, id: post.id };
}
```

Note that `formData.get()` returns `FormDataEntryValue | null` (a string, a File, or null), so always narrow the type before using it.

**Route Handlers:**

```ts
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  const q = request.nextUrl.searchParams.get("q");
  return NextResponse.json({ q });
}
```

**Metadata:**

```ts
import type { Metadata } from "next";

export const metadata: Metadata = { title: "Home" };
```

**Data from your ORM** is usually already typed (Prisma and Drizzle generate or infer types), so avoid hand-writing duplicate types where you can.

---

## 15.3 Environment typing

`process.env` values are typed as `string | undefined` by default. You can declare your known variables once so the editor autocompletes them:

```ts
// env.d.ts
declare namespace NodeJS {
  interface ProcessEnv {
    DATABASE_URL: string;
    AUTH_SECRET: string;
    NEXT_PUBLIC_SITE_URL: string;
  }
}
```

Types do not exist at runtime, so a missing variable is still `undefined`. A safer approach is to **validate at startup** with Zod:

```ts
// lib/env.ts
import { z } from "zod";

const schema = z.object({
  DATABASE_URL: z.string().url(),
  AUTH_SECRET: z.string().min(16),
  NEXT_PUBLIC_SITE_URL: z.string().url(),
});

export const env = schema.parse(process.env);
```

Now the app fails fast with a clear message if configuration is wrong, and `env.DATABASE_URL` is typed as `string`.

Remember:

- Only variables prefixed with `NEXT_PUBLIC_` are available in the browser.
- Keep `.env.local` out of version control.

---

## 15.4 Strictness settings

The `strict` option in `tsconfig.json` enables a bundle of safety checks. Keep it **on**:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "forceConsistentCasingInFileNames": true,
    "paths": { "@/*": ["./*"] }
  }
}
```

What `strict` includes (among others):

| Check | Effect |
|---|---|
| `strictNullChecks` | `null` and `undefined` must be handled explicitly |
| `noImplicitAny` | Every value needs a known type |
| `strictFunctionTypes` | Safer function parameter checking |

Extra options worth considering:

- **`noUncheckedIndexedAccess`**: `array[0]` becomes `T | undefined`, catching out-of-bounds bugs.
- **`noImplicitReturns`**: every code path in a function must return.

Useful commands:

```bash
npx tsc --noEmit   # type-check the whole project without building
```

`next build` also type-checks and fails on errors by default. Avoid the `any` type, prefer `unknown` when a type is truly uncertain, and narrow it before use. Enable ESLint's TypeScript rules for additional protection.

[Previous](./[14]-Authentication-And-Authorization.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[16]-Testing-Nextjs-Applications.md)