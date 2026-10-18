[Previous](./[4]-Server-And-Client-Components.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[6]-Rendering-Strategies.md)

*Data And Rendering*

# Lesson 5 - Data Fetching

## 5.1 Fetching on the server

The recommended way to load data is inside **async Server Components**. There are no `useEffect` hooks, loading flags, or client-side API calls needed.

```tsx
// app/posts/page.tsx
type Post = { id: number; title: string };

export default async function PostsPage() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts");
  if (!res.ok) throw new Error("Failed to fetch posts");
  const posts: Post[] = await res.json();

  return (
    <ul>
      {posts.slice(0, 10).map((p) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  );
}
```

You can also skip HTTP entirely and query a database or read files directly, since this code only runs on the server:

```tsx
import { db } from "@/lib/db";

export default async function Page() {
  const users = await db.user.findMany();
  return <p>{users.length} users</p>;
}
```

If the request throws, the nearest `error.tsx` handles it. Pair this with `loading.tsx` for a smooth experience.

---

## 5.2 Parallel and sequential requests

**Sequential** requests happen when one needs the result of another. If they are independent, awaiting them one after another creates a **waterfall**, which is slow:

```tsx
// Slow: second request waits for the first
const user = await getUser(id);
const posts = await getPosts(id);
```

**Parallel** requests start together using `Promise.all`:

```tsx
// Fast: both start at the same time
const [user, posts] = await Promise.all([getUser(id), getPosts(id)]);
```

Use sequential fetching only when there is a real dependency:

```tsx
const artist = await getArtist(username);       // needed first
const albums = await getAlbums(artist.id);      // depends on artist
```

Another approach is to start promises early and await them later, which lets you stream parts of the page independently (see 5.3).

---

## 5.3 Suspense and streaming

Wrapping a slow component in `<Suspense>` lets the rest of the page render immediately while that part streams in later.

```tsx
// app/dashboard/page.tsx
import { Suspense } from "react";
import Stats from "./Stats";
import RecentOrders from "./RecentOrders";

export default function Dashboard() {
  return (
    <main>
      <h1>Dashboard</h1>
      <Suspense fallback={<p>Loading stats...</p>}>
        <Stats />
      </Suspense>
      <Suspense fallback={<p>Loading orders...</p>}>
        <RecentOrders />
      </Suspense>
    </main>
  );
}
```

```tsx
// app/dashboard/Stats.tsx (async Server Component)
export default async function Stats() {
  const stats = await getStats(); // slow
  return <p>Revenue: {stats.revenue}</p>;
}
```

Each boundary resolves independently, so a slow query no longer blocks the whole page. `loading.tsx` is simply a Suspense boundary around the entire page; manual `<Suspense>` gives you finer control.

You can also pass a promise from a Server Component to a Client Component and read it with React's `use` hook:

```tsx
// Server
<Suspense fallback={<p>Loading...</p>}>
  <Comments commentsPromise={getComments()} />
</Suspense>
```

```tsx
// Client
"use client";
import { use } from "react";

export default function Comments({ commentsPromise }: { commentsPromise: Promise<string[]> }) {
  const comments = use(commentsPromise);
  return <ul>{comments.map((c) => <li key={c}>{c}</li>)}</ul>;
}
```

---

## 5.4 Fetching in Client Components

Sometimes data must be fetched in the browser: live updates, polling, user-triggered searches, or data depending on client-only state. Use a library such as **SWR** or **TanStack Query** rather than raw `useEffect`.

```tsx
"use client";

import useSWR from "swr";

const fetcher = (url: string) => fetch(url).then((r) => r.json());

export default function Profile() {
  const { data, error, isLoading } = useSWR("/api/profile", fetcher);

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Failed to load</p>;
  return <h1>Hello, {data.name}</h1>;
}
```

These libraries give you caching, deduplication, revalidation on focus, and error handling out of the box.

A good rule of thumb: **fetch on the server by default**, and fetch on the client only when the data truly depends on browser interaction or needs to refresh continuously.

[Previous](./[4]-Server-And-Client-Components.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[6]-Rendering-Strategies.md)