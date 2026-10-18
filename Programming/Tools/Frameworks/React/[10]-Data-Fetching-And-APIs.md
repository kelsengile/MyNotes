[Previous](./[9]-Routing-With-React-Router.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[11]-Styling-React-Apps.md)

*Building Applications*

# Lesson 10 - Data Fetching And APIs

## 10.1 Fetching with fetch and Axios

Most apps load data from a server **API** (usually returning JSON). The browser has a built-in `fetch` function, and the popular library **Axios** offers a friendlier alternative.

### fetch

```js
async function getPosts() {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts");

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();     // a Promise resolving to parsed data
}
```

**Important:** `fetch` only rejects its promise on network failure. A `404` or `500` response does *not* throw, so you must check `response.ok`.

Sending data:

```js
await fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ title: "Hello", body: "World", userId: 1 }),
});
```

### Axios

```bash
npm install axios
```

```js
import axios from "axios";

const { data } = await axios.get("https://jsonplaceholder.typicode.com/posts");

await axios.post("https://jsonplaceholder.typicode.com/posts", {
  title: "Hello",
});
```

Compared with `fetch`, Axios automatically parses JSON, throws on non-2xx status codes, and supports **instances** with shared settings:

```js
const api = axios.create({
  baseURL: "https://api.example.com",
  timeout: 5000,
});
api.get("/users");
```

### Where to call them

In React, trigger a request:

- from an **event handler** (user clicks "Load"), or
- from an **effect** (load when the component appears), as in [Lesson 5](./[5]-Effects-And-Lifecycle.md), or
- through a **data library** (10.3), which is recommended for most apps.

---

## 10.2 Loading and error states

Every network request has three possible situations that your UI must show: **loading**, **error**, and **success**.

```jsx
import { useEffect, useState } from "react";

function PostList() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    async function load() {
      try {
        const res = await fetch(
          "https://jsonplaceholder.typicode.com/posts?_limit=10",
          { signal: controller.signal }
        );
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        setPosts(await res.json());
      } catch (err) {
        if (err.name !== "AbortError") setError(err.message);
      } finally {
        if (!controller.signal.aborted) setLoading(false);
      }
    }

    load();
    return () => controller.abort();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p role="alert">Something went wrong: {error}</p>;
  if (posts.length === 0) return <p>No posts yet.</p>;

  return (
    <ul>
      {posts.map((p) => <li key={p.id}>{p.title}</li>)}
    </ul>
  );
}
```

Points to cover in every data component:

- **Loading**: show a spinner or skeleton.
- **Error**: show a clear message and, ideally, a retry button.
- **Empty**: distinguish "no results" from "still loading".
- **Success**: render the data.

### Common problems

- **Forgetting cleanup** causes race conditions and state updates after unmounting.
- **Duplicated code**: every component repeats loading/error logic. Extract a `useFetch` hook (see [Lesson 7](./[7]-More-Built-In-Hooks.md)) or use a library.
- **No caching**: the same data is re-fetched every time a component mounts.

---

## 10.3 TanStack Query and caching

**TanStack Query** (formerly React Query) manages **server state**: fetching, caching, background refreshing, retries, and deduplicating requests.

```bash
npm install @tanstack/react-query
```

### Setup

Create one `QueryClient` and provide it:

```jsx
// main.jsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient();

createRoot(document.getElementById("root")).render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

### Reading data with useQuery

```jsx
import { useQuery } from "@tanstack/react-query";

async function fetchPosts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=10");
  if (!res.ok) throw new Error("Failed to fetch posts");
  return res.json();
}

function PostList() {
  const { data, isPending, isError, error, refetch } = useQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
  });

  if (isPending) return <p>Loading...</p>;
  if (isError) return (
    <div role="alert">
      <p>Error: {error.message}</p>
      <button onClick={() => refetch()}>Retry</button>
    </div>
  );

  return (
    <ul>
      {data.map((p) => <li key={p.id}>{p.title}</li>)}
    </ul>
  );
}
```

All the state handling from 10.2 is replaced by a few lines.

- **`queryKey`**: a unique array identifying the data. Include any variables the query depends on, for example `["post", postId]`. When the key changes, a new fetch happens automatically.
- **`queryFn`**: a function returning a Promise of the data. It must throw on errors.

### Caching behavior

TanStack Query stores results by `queryKey`. When a component mounts and data is already cached, it shows the cached data immediately and may refresh it in the background. Two key settings:

- **`staleTime`**: how long data is considered fresh (default `0`, meaning stale immediately).
- **`gcTime`**: how long unused cached data stays in memory (default 5 minutes).

By default it also refetches stale data when the window regains focus or the network reconnects.

### Changing data with useMutation

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

function AddPost() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newPost) =>
      fetch("https://jsonplaceholder.typicode.com/posts", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(newPost),
      }).then((r) => r.json()),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["posts"] });  // refetch the list
    },
  });

  return (
    <button
      onClick={() => mutation.mutate({ title: "New", body: "Post", userId: 1 })}
      disabled={mutation.isPending}
    >
      {mutation.isPending ? "Saving..." : "Add post"}
    </button>
  );
}
```

`invalidateQueries` marks cached data as stale so the list refreshes with the server's latest data.

---

## 10.4 Suspense for data

**Suspense** lets a component declare "I'm not ready yet", and lets a parent show a **fallback** while waiting.

```jsx
import { Suspense } from "react";

<Suspense fallback={<p>Loading...</p>}>
  <Comments />
</Suspense>
```

If `Comments` (or anything below it) is waiting for data, the fallback appears in its place. Loading states are therefore handled by the parent, not by each component.

### Error handling with Suspense

Pair it with an **error boundary** (see [Lesson 16](./[16]-Accessibility-And-Best-Practices.md)) to handle failures:

```jsx
<ErrorBoundary fallback={<p>Could not load comments.</p>}>
  <Suspense fallback={<p>Loading...</p>}>
    <Comments />
  </Suspense>
</ErrorBoundary>
```

### The `use` API (React 19)

`use` reads the value of a Promise during render, and suspends until it resolves:

```jsx
import { use, Suspense } from "react";

function Comments({ commentsPromise }) {
  const comments = use(commentsPromise);   // suspends until resolved
  return (
    <ul>
      {comments.map((c) => <li key={c.id}>{c.body}</li>)}
    </ul>
  );
}

function Post({ id }) {
  // The Promise must be stable between renders (created by a parent,
  // a cache, a router loader, or a framework), not created inside Comments.
  const commentsPromise = getCommentsCached(id);

  return (
    <Suspense fallback={<p>Loading comments...</p>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  );
}
```

(`getCommentsCached` is a placeholder for any function that returns the same Promise for the same `id`.)

**Warning:** creating a *new* Promise inside the component that calls `use` causes it to suspend again on every render. Create promises outside, cache them, or let a library or framework do it.

### Suspense with TanStack Query

TanStack Query provides a Suspense-ready hook so you don't manage Promises yourself:

```jsx
import { useSuspenseQuery } from "@tanstack/react-query";

function Posts() {
  const { data } = useSuspenseQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
  });
  return <ul>{data.map((p) => <li key={p.id}>{p.title}</li>)}</ul>;
}

<Suspense fallback={<p>Loading posts...</p>}>
  <Posts />
</Suspense>
```

With `useSuspenseQuery`, `data` is always defined, so there is no `isPending` check.

### Which approach should I use?

| Approach | Best for |
|---|---|
| `useEffect` + `fetch` | Learning, tiny projects |
| TanStack Query | Most client-rendered apps |
| Suspense + `use` / router loaders / framework data loading | Apps using a framework or router that supplies stable promises |

[Previous](./[9]-Routing-With-React-Router.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[11]-Styling-React-Apps.md)