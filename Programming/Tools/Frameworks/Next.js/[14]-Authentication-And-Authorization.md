[Previous](./[13]-Databases-And-ORMs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[15]-TypeScript-With-Nextjs.md)

*Building Features*

# Lesson 14 - Authentication And Authorization

## 14.1 Sessions and tokens

**Authentication** answers "who are you?". **Authorization** answers "what are you allowed to do?". They are separate steps.

After a user logs in, the app must remember them across requests. Two common approaches:

| | **Database sessions** | **Stateless tokens (JWT)** |
|---|---|---|
| What is stored in the cookie | A random session ID | A signed token containing user data |
| Where the data lives | Database (or cache) | Inside the token itself |
| Revoking access | Easy (delete the session) | Harder (wait for expiry or keep a blocklist) |
| Database lookup per request | Yes | No |

In both cases the identifier is usually stored in a cookie with safe settings:

```ts
import { cookies } from "next/headers";

export async function setSessionCookie(value: string) {
  const cookieStore = await cookies();
  cookieStore.set("session", value, {
    httpOnly: true,   // not readable by JavaScript
    secure: true,     // HTTPS only
    sameSite: "lax",  // helps against CSRF
    path: "/",
    maxAge: 60 * 60 * 24 * 7, // 7 days
  });
}
```

Security essentials:

- Never store passwords in plain text; hash them with bcrypt or argon2.
- Never put secrets in `NEXT_PUBLIC_` variables.
- Always use `httpOnly` and `secure` cookies for session data.

---

## 14.2 Auth.js

**Auth.js** (formerly NextAuth.js) is a popular authentication library for Next.js. It supports OAuth providers (Google, GitHub), email links, and username/password logins through credentials. Setup details change between versions, so follow the current Auth.js docs. The shape below follows the v5 style.

```ts
// auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [GitHub],
});
```

```ts
// app/api/auth/[...nextauth]/route.ts
import { handlers } from "@/auth";

export const { GET, POST } = handlers;
```

Read the current session in a Server Component:

```tsx
import { auth } from "@/auth";

export default async function Profile() {
  const session = await auth();
  if (!session?.user) return <p>You are not signed in.</p>;
  return <h1>Hello, {session.user.name}</h1>;
}
```

Sign in and out with Server Actions:

```tsx
import { signIn, signOut } from "@/auth";

export function SignInButton() {
  return (
    <form action={async () => { "use server"; await signIn("github"); }}>
      <button type="submit">Sign in with GitHub</button>
    </form>
  );
}

export function SignOutButton() {
  return (
    <form action={async () => { "use server"; await signOut(); }}>
      <button type="submit">Sign out</button>
    </form>
  );
}
```

Environment variables (such as a secret and the provider's client ID and secret) are required; check the docs for exact names.

---

## 14.3 Protecting routes

Protect your app in **layers**. Do not rely on just one.

**Layer 1: Middleware** for fast, optimistic redirects:

```ts
// middleware.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const hasSession = request.cookies.has("session");
  if (!hasSession && request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }
  return NextResponse.next();
}

export const config = { matcher: ["/dashboard/:path*"] };
```

This only checks that a cookie **exists**, so it is not proof of a valid user.

**Layer 2: Check in the page or layout** by verifying the real session:

```tsx
import { redirect } from "next/navigation";
import { auth } from "@/auth";

export default async function DashboardPage() {
  const session = await auth();
  if (!session) redirect("/login");
  return <h1>Dashboard</h1>;
}
```

**Layer 3: Check in the data layer**, close to where data is read or written:

```ts
// lib/dal.ts
import "server-only";
import { auth } from "@/auth";

export async function getMyOrders() {
  const session = await auth();
  if (!session?.user) throw new Error("Unauthorized");
  return db.order.findMany({ where: { userId: session.user.id } });
}
```

Important: **always verify authorization inside Server Actions and Route Handlers**, because they can be called directly by anyone, regardless of what your pages show.

---

## 14.4 Role-based access

**Role-based access control (RBAC)** grants permissions based on a user's role, such as `user`, `editor`, or `admin`.

Store the role on the user record and expose it on the session:

```ts
type Role = "user" | "editor" | "admin";

type SessionUser = {
  id: string;
  name: string;
  role: Role;
};
```

Create a reusable guard:

```ts
// lib/guards.ts
import "server-only";
import { redirect } from "next/navigation";
import { auth } from "@/auth";

export async function requireRole(allowed: Role[]) {
  const session = await auth();
  if (!session?.user) redirect("/login");
  if (!allowed.includes(session.user.role)) redirect("/unauthorized");
  return session.user;
}
```

Use it in pages and actions:

```tsx
// app/admin/page.tsx
import { requireRole } from "@/lib/guards";

export default async function AdminPage() {
  await requireRole(["admin"]);
  return <h1>Admin panel</h1>;
}
```

```ts
// app/actions.ts
"use server";

export async function deleteUser(id: string) {
  await requireRole(["admin"]); // check inside the action itself
  await db.user.delete({ where: { id } });
}
```

You can also hide UI by role (for example, only showing a delete button to admins), but treat that as **cosmetic**. The real protection must always be enforced on the server.

Principle of least privilege: give each role only the permissions it needs, and deny by default.

[Previous](./[13]-Databases-And-ORMs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[15]-TypeScript-With-Nextjs.md)