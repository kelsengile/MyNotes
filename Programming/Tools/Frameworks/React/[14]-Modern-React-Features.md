[Previous](./[13]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[15]-Testing-React-Apps.md)

*Going Further*

# Lesson 14 - Modern React Features

## 14.1 Concurrent rendering and transitions

### What "concurrent" means

In older React, once a render started it had to finish before anything else could happen. **Concurrent rendering** lets React **pause, resume, or abandon** a render so urgent work, such as typing, can interrupt less urgent work, such as re-drawing a big list. It is built into React 18 and later; you opt in to its benefits by marking updates as non-urgent.

### useTransition

`useTransition` marks a state update as a **transition**: low priority, interruptible.

```jsx
import { useState, useTransition } from "react";

function Search({ allItems }) {
  const [query, setQuery] = useState("");
  const [filter, setFilter] = useState("");
  const [isPending, startTransition] = useTransition();

  function handleChange(e) {
    setQuery(e.target.value);              // urgent: input stays responsive
    startTransition(() => {
      setFilter(e.target.value);           // non-urgent: heavy list update
    });
  }

  const results = allItems.filter((i) => i.includes(filter));

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending && <p>Updating...</p>}
      <ul>
        {results.map((r) => <li key={r}>{r}</li>)}
      </ul>
    </>
  );
}
```

- `startTransition` wraps the non-urgent state updates.
- `isPending` is `true` while the transition is in progress, useful for a subtle loading hint.
- If the user types again, React can abandon the in-progress transition and start over.

Transitions also avoid hiding already-visible content behind a Suspense fallback; React keeps showing the old UI while the new one prepares.

React 19 allows **async functions** inside transitions: `startTransition(async () => { await save(); })`. `isPending` stays `true` until it finishes. Note that any state updates made *after* an `await` inside the function need their own `startTransition` wrapper.

### useDeferredValue

`useDeferredValue` gives you a **lagging copy** of a value that updates after urgent work finishes:

```jsx
import { useDeferredValue, useState } from "react";

function Search({ allItems }) {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);

  const results = allItems.filter((i) => i.includes(deferredQuery));

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ResultsList results={results} />
    </>
  );
}
```

Use `useTransition` when you control the code that sets the state. Use `useDeferredValue` when the value comes from props or elsewhere.

---

## 14.2 Server Components

**React Server Components (RSC)** are components that run **only on the server** (at build time or per request), never in the browser. Their code and dependencies are **not sent to the client**, which reduces JavaScript size.

### Important: RSC needs a framework or bundler support

Server Components are not something you turn on in a plain Vite SPA. They require a framework (for example **Next.js App Router**) or a compatible bundler setup. Treat this section as a conceptual introduction.

### Two kinds of components

| | Server Component | Client Component |
|---|---|---|
| Where it runs | Server only | Browser (and is pre-rendered on the server) |
| Can use state, effects, event handlers | No | Yes |
| Can access database / filesystem directly | Yes | No |
| Code sent to browser | No | Yes |
| Marked with | (default in RSC frameworks) | `"use client"` at the top of the file |

### A Server Component

```jsx
// Page.jsx: Server Component (async allowed)
import { db } from "./db.js";
import LikeButton from "./LikeButton.jsx";

export default async function Page({ id }) {
  const post = await db.posts.find(id);    // direct data access on the server

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
      <LikeButton postId={post.id} />        {/* a Client Component */}
    </article>
  );
}
```

(`db` is a placeholder for your own server-side data access code.)

### A Client Component

```jsx
// LikeButton.jsx
"use client";

import { useState } from "react";

export default function LikeButton({ postId }) {
  const [liked, setLiked] = useState(false);
  return (
    <button onClick={() => setLiked(!liked)}>
      {liked ? "Liked" : "Like"}
    </button>
  );
}
```

The `"use client"` directive marks the **boundary**: that file and everything it imports run on the client.

### Rules of thumb

- Server Components can **import and render** Client Components, and can pass them **serializable props** (strings, numbers, plain objects, and so on, but not functions).
- Client Components **cannot import** Server Components, but can receive them as `children`.
- Default to Server Components, and add `"use client"` only where you need interactivity.

### Server Functions

A related feature, **Server Functions**, marked with `"use server"`, lets client code call a function that runs on the server:

```jsx
"use server";

export async function saveNote(formData) {
  const text = formData.get("text");
  // save to a database...
}
```

Never assume server code is safe just because it is called from your own UI: **validate input and check authorization inside every Server Function**, since it is a public endpoint.

---

## 14.3 Actions and form handling

React 19 introduced **Actions**: functions that handle async work (such as submitting a form) and automatically manage the pending state, errors, and optimistic updates.

### The form action prop

You can pass a function to a `<form>`'s `action` prop. React calls it with the `FormData` and resets uncontrolled fields after success:

```jsx
function AddComment() {
  async function addComment(formData) {
    const text = formData.get("text");
    await fetch("/api/comments", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ text }),
    });
  }

  return (
    <form action={addComment}>
      <input name="text" required />
      <button type="submit">Post</button>
    </form>
  );
}
```

### useActionState

Tracks an action's result and pending status:

```jsx
import { useActionState } from "react";

async function signup(previousState, formData) {
  const email = formData.get("email");
  if (!email.includes("@")) {
    return { error: "Please enter a valid email." };
  }
  // await api.signup(email) ...
  return { success: true };
}

function SignupForm() {
  const [state, formAction, isPending] = useActionState(signup, {});

  return (
    <form action={formAction}>
      <input name="email" type="email" />
      <button disabled={isPending}>
        {isPending ? "Signing up..." : "Sign up"}
      </button>
      {state.error && <p role="alert">{state.error}</p>}
      {state.success && <p>Thanks for signing up!</p>}
    </form>
  );
}
```

The action receives the **previous state** first and the `FormData` second. Whatever it returns becomes the new `state`.

### useFormStatus

A component **inside** a form can read the form's status without props. It must be a child of the `<form>`:

```jsx
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>{pending ? "Saving..." : "Save"}</button>;
}
```

### useOptimistic

Shows an **expected result immediately** while the real request is still running, then reconciles with the real result:

```jsx
import { useOptimistic, useState } from "react";

function Messages({ initialMessages, sendMessage }) {
  const [messages, setMessages] = useState(initialMessages);
  const [optimisticMessages, addOptimistic] = useOptimistic(
    messages,
    (current, newText) => [...current, { id: "temp", text: newText, sending: true }]
  );

  async function action(formData) {
    const text = formData.get("text");
    addOptimistic(text);                       // appears instantly
    const saved = await sendMessage(text);     // real request
    setMessages((prev) => [...prev, saved]);   // replace with real data
  }

  return (
    <>
      <ul>
        {optimisticMessages.map((m) => (
          <li key={m.id} style={{ opacity: m.sending ? 0.5 : 1 }}>{m.text}</li>
        ))}
      </ul>
      <form action={action}>
        <input name="text" />
        <button>Send</button>
      </form>
    </>
  );
}
```

If the request fails, the optimistic state is discarded automatically and the UI returns to the real state.

### Summary

| API | Purpose |
|---|---|
| `<form action={fn}>` | Run a function on submit |
| `useActionState` | Result and pending state of an action |
| `useFormStatus` | Pending state of the parent form, for child components |
| `useOptimistic` | Instant UI before the server responds |

---

## 14.4 The React Compiler

The **React Compiler** is a build-time tool that **automatically memoizes** your components and hooks. It analyzes your code and inserts the caching that you would otherwise write by hand with `useMemo`, `useCallback`, and `React.memo` (see [Lesson 13](./[13]-Performance-Optimization.md)).

```jsx
// You write simple code...
function TodoList({ todos, filter }) {
  const visible = todos.filter((t) => matches(t, filter));
  return <List items={visible} />;
}
// ...the compiler adds the memoization where it is safe and useful.
```

### Requirements

The compiler relies on your code following the **Rules of React**:

- Components and hooks are **pure** during render.
- Props and state are **never mutated**.
- Hooks are called only at the top level.

Code that breaks these rules may be skipped by the compiler or behave unexpectedly. The ESLint plugin `eslint-plugin-react-hooks` includes rules that help find these problems.

### Enabling it in Vite

```bash
npm install -D babel-plugin-react-compiler
```

```js
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [
    react({
      babel: {
        plugins: [["babel-plugin-react-compiler"]],
      },
    }),
  ],
});
```

Setup details vary by React version, bundler, and plugin version. If this snippet does not match your template, check the official React Compiler documentation at [react.dev](https://react.dev) for current instructions. Some project templates also offer a compiler option when you create the project.

### Practical advice

- You can adopt it **incrementally**, for example only on certain folders, and keep existing `useMemo`/`useCallback` calls; they are not harmful.
- For new code, write clear, simple components first and rely on the compiler for memoization.
- Still **measure** performance; the compiler reduces unnecessary re-renders but does not fix slow algorithms or large lists.
- Use the **React DevTools** to see which components the compiler optimized.

[Previous](./[13]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[15]-Testing-React-Apps.md)