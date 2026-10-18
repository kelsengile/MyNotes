[Previous](./[12]-Middleware-And-Edge.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[14]-Authentication-And-Authorization.md)

*Building Features*

# Lesson 13 - Databases And ORMs

## 13.1 Connecting to a database

Because Server Components, Server Actions, and Route Handlers run on the server, they can talk to a database directly. Next.js does not include a database; you choose one:

| Type | Examples |
|---|---|
| Relational (SQL) | PostgreSQL, MySQL, SQLite |
| Document (NoSQL) | MongoDB |
| Hosted/serverless | Neon, Supabase, PlanetScale, Turso, Vercel Postgres |

Store the connection string in an environment variable, never in code:

```bash
# .env.local  (never commit this file)
DATABASE_URL="postgresql://user:password@localhost:5432/mydb"
```

Access it on the server with `process.env.DATABASE_URL`. Variables **without** the `NEXT_PUBLIC_` prefix are never exposed to the browser.

A key concern is **connection management**. In development, hot reloading can create many database connections. In serverless deployments, each function instance may open its own. Use a single shared client (see 13.2) and, when serverless, a connection pooler or a serverless-friendly driver.

---

## 13.2 Prisma and Drizzle

An **ORM** (Object-Relational Mapper) lets you work with the database using typed code rather than raw SQL. Two popular choices:

| | **Prisma** | **Drizzle** |
|---|---|---|
| Style | Schema file + generated client | Schema in TypeScript, SQL-like queries |
| Learning curve | Gentle | Slightly steeper if new to SQL |
| Type safety | Generated types | Inferred from your schema |
| Best for | Fast development, clear abstractions | SQL control, lightweight setup |

**Prisma** schema (`prisma/schema.prisma`):

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  createdAt DateTime @default(now())
}
```

Create a **shared client** so hot reloading does not open new connections each time:

```ts
// lib/db.ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as { prisma?: PrismaClient };

export const db = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db;
```

**Drizzle** schema (TypeScript):

```ts
// db/schema.ts
import { pgTable, serial, text, boolean, timestamp } from "drizzle-orm/pg-core";

export const posts = pgTable("posts", {
  id: serial("id").primaryKey(),
  title: text("title").notNull(),
  content: text("content"),
  published: boolean("published").default(false),
  createdAt: timestamp("created_at").defaultNow(),
});
```

Setup details differ between ORM versions, so follow each tool's current documentation.

---

## 13.3 Querying in Server Components

Import your database client and query inside an `async` Server Component:

```tsx
// app/posts/page.tsx
import { db } from "@/lib/db";

export default async function PostsPage() {
  const posts = await db.post.findMany({
    where: { published: true },
    orderBy: { createdAt: "desc" },
  });

  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

Inside a dynamic route:

```tsx
// app/posts/[id]/page.tsx
import { notFound } from "next/navigation";
import { db } from "@/lib/db";

export default async function PostPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;
  const post = await db.post.findUnique({ where: { id: Number(id) } });
  if (!post) notFound();
  return <h1>{post.title}</h1>;
}
```

Writing data through a Server Action:

```ts
// app/actions.ts
"use server";

import { revalidatePath } from "next/cache";
import { db } from "@/lib/db";

export async function createPost(formData: FormData) {
  await db.post.create({ data: { title: String(formData.get("title")) } });
  revalidatePath("/posts");
}
```

Best practices:

- Put queries in dedicated functions (for example `lib/queries.ts`) instead of scattering them across components.
- Select only the fields you need.
- Add `import "server-only"` to data modules so they can never be bundled into client code.
- Avoid query waterfalls by running independent queries with `Promise.all`.

---

## 13.4 Migrations

A **migration** is a versioned change to your database structure (adding a table, column, or index). Migrations keep every environment's schema in sync and are saved in your repository.

**Prisma:**

```bash
# Create and apply a migration in development
npx prisma migrate dev --name add-posts

# Apply existing migrations in production
npx prisma migrate deploy

# Regenerate the client after schema changes
npx prisma generate
```

**Drizzle** uses `drizzle-kit`:

```bash
npx drizzle-kit generate   # create SQL migration files from schema changes
npx drizzle-kit migrate    # apply migrations
```

Workflow:

1. Edit your schema file.
2. Generate a migration.
3. Review the generated SQL.
4. Apply it locally, then commit the migration files.
5. Apply it in production as part of deployment.

Cautions:

- Never edit a migration that has already been applied elsewhere; create a new one.
- Be careful with destructive changes (dropping columns) because they delete data. Back up first.
- Run `migrate deploy` (not `migrate dev`) in production.

[Previous](./[12]-Middleware-And-Edge.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[14]-Authentication-And-Authorization.md)