[Previous](./[11]-Optimization-And-Metadata.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[13]-Databases-And-ORMs.md)

*Building Features*

# Lesson 12 - Middleware And Edge

## 12.1 What middleware does

**Middleware** is code that runs **before a request is completed**, so it can inspect the request and then redirect, rewrite, modify headers, or let it continue. It sits in front of your routes.

Create a `middleware.ts` file at the project root (or inside `src/` if you use it):

```ts
// middleware.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const response = NextResponse.next();
  response.headers.set("x-custom-header", "hello");
  return response;
}
```

Typical uses:

- Redirecting unauthenticated users to a login page
- Redirecting by country or language
- A/B testing through rewrites
- Setting security or custom headers
- Basic rate limiting or bot filtering

Note: newer Next.js releases rename this file convention to `proxy.ts` (with an exported `proxy` function). The concept is the same, so check the docs for your installed version.

Keep middleware **fast and light**, because it runs on many requests. Avoid heavy computation or slow database queries.

---

## 12.2 Matching requests

By default, middleware runs on **every** request, including static files and images. Use a `matcher` to limit it:

```ts
export const config = {
  matcher: ["/dashboard/:path*", "/account/:path*"],
};
```

Matcher patterns:

| Pattern | Matches |
|---|---|
| `/about` | Exactly `/about` |
| `/blog/:slug` | One dynamic segment |
| `/dashboard/:path*` | `/dashboard` and everything below |
| `/((?!api\|_next/static\|_next/image\|favicon.ico).*)` | Everything except those prefixes |

You can also filter inside the function with conditions:

```ts
export function middleware(request: NextRequest) {
  if (request.nextUrl.pathname.startsWith("/admin")) {
    // admin-only logic
  }
  return NextResponse.next();
}
```

Matchers must be constant values, since they are analyzed at build time.

---

## 12.3 Redirects and rewrites

A **redirect** sends the browser to a new URL, and the address bar changes. A **rewrite** serves different content while the address bar stays the same.

```ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const token = request.cookies.get("session")?.value;
  const { pathname } = request.nextUrl;

  // Redirect: protect the dashboard
  if (pathname.startsWith("/dashboard") && !token) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  // Rewrite: serve a different page at the same URL
  if (pathname === "/promo") {
    return NextResponse.rewrite(new URL("/campaigns/summer", request.url));
  }

  return NextResponse.next();
}
```

Reading and setting cookies:

```ts
const response = NextResponse.next();
response.cookies.set("visited", "true", { httpOnly: true, path: "/" });
return response;
```

| Action | Address bar | Use for |
|---|---|---|
| `redirect` | Changes | Login redirects, moved pages |
| `rewrite` | Stays the same | A/B tests, proxying, clean URLs |
| `next` | Stays the same | Continue, optionally with new headers |

---

## 12.4 Edge vs Node runtime

Next.js can run code in two environments:

| | **Edge runtime** | **Node.js runtime** |
|---|---|---|
| Based on | Lightweight Web APIs | Full Node.js |
| Startup | Very fast | Slower cold start |
| Location | Close to users (globally distributed) | Regional server |
| Node APIs (`fs`, many npm packages) | Not available | Available |
| Code size limits | Small | Larger |
| Best for | Lightweight request logic | Database access, heavy work |

Choose the runtime for a route handler or page with a segment config:

```ts
export const runtime = "edge"; // or "nodejs"
```

Historically, middleware always ran on the Edge runtime, which is why it could not use many Node libraries (such as full database drivers). Newer Next.js versions add support for running this layer on the Node.js runtime. The available options depend on your version, so confirm in the docs.

Practical advice: use Node.js unless you have a specific reason for the edge, such as very low latency for simple logic. Because of these limitations, do **real** authorization checks close to your data, not only in middleware (see Lesson 14).

[Previous](./[11]-Optimization-And-Metadata.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[13]-Databases-And-ORMs.md)