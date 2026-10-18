[Previous](./[8]-Server-Actions-And-Forms.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[10]-Styling-In-Nextjs.md)

*Data And Rendering*

# Lesson 9 - Route Handlers And APIs

## 9.1 Creating route handlers

A **Route Handler** is a custom request handler for a route, built with the standard Web `Request` and `Response` APIs. You create one by adding a `route.ts` file inside the `app` directory.

```
app/
└── api/
    └── hello/
        └── route.ts    →  /api/hello
```

```ts
// app/api/hello/route.ts
export async function GET() {
  return Response.json({ message: "Hello from the API" });
}
```

Visit `/api/hello` and you receive JSON. Rules:

- A `route.ts` cannot live in the same folder as a `page.tsx` (they would both claim the same URL).
- Route Handlers run on the server only.
- They can be placed at any depth, not just under `api/`.

---

## 9.2 HTTP methods and responses

Export a function named after the HTTP method: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, or `OPTIONS`.

```ts
// app/api/posts/route.ts
import { NextResponse } from "next/server";

export async function GET() {
  const posts = await db.post.findMany();
  return NextResponse.json(posts);
}

export async function POST(request: Request) {
  const body = await request.json();

  if (!body.title) {
    return NextResponse.json({ error: "Title is required" }, { status: 400 });
  }

  const post = await db.post.create({ data: { title: body.title } });
  return NextResponse.json(post, { status: 201 });
}
```

Common status codes:

| Code | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 400 | Bad request |
| 401 | Unauthorized |
| 404 | Not found |
| 500 | Server error |

`NextResponse` extends the standard `Response` with helpers for cookies, redirects, and rewrites. You can also return plain text, streams, or files with `new Response(...)`.

In recent versions, `GET` handlers are not cached by default. In older versions they could be cached; check the docs for your version.

---

## 9.3 Working with request data

**Query parameters:**

```ts
// GET /api/search?q=next
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const q = searchParams.get("q");
  return Response.json({ query: q });
}
```

**Dynamic segments** (the `params` is a Promise in recent versions):

```ts
// app/api/posts/[id]/route.ts
export async function GET(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
  const post = await db.post.findUnique({ where: { id } });
  if (!post) return Response.json({ error: "Not found" }, { status: 404 });
  return Response.json(post);
}
```

**Request body:**

```ts
const json = await request.json();        // JSON
const form = await request.formData();    // form data
const text = await request.text();        // raw text
```

**Headers and cookies:**

```ts
import { cookies, headers } from "next/headers";

export async function GET() {
  const headerList = await headers();
  const cookieStore = await cookies();

  const auth = headerList.get("authorization");
  const session = cookieStore.get("session");

  return Response.json({ hasAuth: !!auth, hasSession: !!session });
}
```

Always validate incoming data and check authentication, since route handlers are public endpoints.

---

## 9.4 When to use handlers vs actions

| Use a **Server Action** when... | Use a **Route Handler** when... |
|---|---|
| Submitting a form from your own app | Building a public API used by other clients |
| Mutating data (create, update, delete) | Receiving webhooks (Stripe, GitHub, etc.) |
| You want built-in revalidation and progressive enhancement | You need a custom response format (files, streams, XML) |
| Only your Next.js UI calls it | A mobile app or third-party service calls it |
| You prefer typed function calls | You need specific HTTP methods, headers, or status codes |

Practical guidance:

- **Reading data for pages**: do it directly in Server Components, with no API layer needed.
- **Mutations from your UI**: prefer Server Actions.
- **External consumers or integrations**: use Route Handlers.
- Avoid calling your own Route Handlers from Server Components; call the shared function directly instead, since it saves a network round trip.

[Previous](./[8]-Server-Actions-And-Forms.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[10]-Styling-In-Nextjs.md)