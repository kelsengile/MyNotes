[Previous](./[7]-Caching-And-Revalidation.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[9]-Route-Handlers-And-APIs.md)

*Data And Rendering*

# Lesson 8 - Server Actions And Forms

## 8.1 Defining server actions

A **Server Action** is an async function that runs on the server but can be called from the client, with no manual API route needed. Next.js creates the network request for you.

Mark a function with the `"use server"` directive.

**Inline** (inside a Server Component):

```tsx
export default function Page() {
  async function saveName(formData: FormData) {
    "use server";
    const name = formData.get("name");
    await db.user.create({ data: { name: String(name) } });
  }

  return (
    <form action={saveName}>
      <input name="name" />
      <button type="submit">Save</button>
    </form>
  );
}
```

**In a separate file** (required if a Client Component needs to import it):

```ts
// app/actions.ts
"use server";

export async function deletePost(id: string) {
  await db.post.delete({ where: { id } });
}
```

Important: Server Actions are exposed as public HTTP endpoints. Always **validate input** and **check authentication and authorization** inside every action, exactly as you would in an API route.

---

## 8.2 Handling forms

Passing a Server Action to a form's `action` prop makes the form submit to it. The action receives a `FormData` object.

```ts
// app/actions.ts
"use server";

import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";

export async function createPost(formData: FormData) {
  const title = String(formData.get("title"));
  const content = String(formData.get("content"));

  await db.post.create({ data: { title, content } });

  revalidatePath("/posts"); // refresh cached list
  redirect("/posts");       // go to the list
}
```

```tsx
// app/posts/new/page.tsx
import { createPost } from "@/app/actions";

export default function NewPost() {
  return (
    <form action={createPost}>
      <input name="title" placeholder="Title" required />
      <textarea name="content" placeholder="Content" />
      <button type="submit">Publish</button>
    </form>
  );
}
```

A benefit of this approach is **progressive enhancement**: forms using Server Actions can work even before JavaScript loads in the browser.

To pass extra arguments besides form data, use `bind`:

```tsx
const updateWithId = updatePost.bind(null, post.id);
// <form action={updateWithId}>
```

You can also call an action from an event handler in a Client Component, for example `onClick={() => deletePost(id)}`.

---

## 8.3 Pending and optimistic UI

**Pending state with `useFormStatus`.** This hook must be used in a component rendered **inside** the `<form>`:

```tsx
"use client";

import { useFormStatus } from "react-dom";

export function SubmitButton() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? "Saving..." : "Save"}
    </button>
  );
}
```

**Pending state with `useTransition`** when calling an action from a handler:

```tsx
"use client";

import { useTransition } from "react";
import { deletePost } from "@/app/actions";

export function DeleteButton({ id }: { id: string }) {
  const [isPending, startTransition] = useTransition();
  return (
    <button
      disabled={isPending}
      onClick={() => startTransition(() => deletePost(id))}
    >
      {isPending ? "Deleting..." : "Delete"}
    </button>
  );
}
```

**Optimistic UI with `useOptimistic`** shows the expected result immediately, before the server confirms:

```tsx
"use client";

import { useOptimistic } from "react";
import { addTodo } from "@/app/actions";

export function Todos({ todos }: { todos: string[] }) {
  const [optimisticTodos, addOptimistic] = useOptimistic(
    todos,
    (current, newTodo: string) => [...current, newTodo]
  );

  async function action(formData: FormData) {
    const text = String(formData.get("text"));
    addOptimistic(text);   // appears instantly
    await addTodo(text);   // real save
  }

  return (
    <form action={action}>
      <input name="text" />
      <ul>{optimisticTodos.map((t, i) => <li key={i}>{t}</li>)}</ul>
    </form>
  );
}
```

If the action fails, React rolls back to the real state.

---

## 8.4 Validation and errors

Never trust form data. Validate on the server, commonly with **Zod**, and return errors to the UI instead of throwing for expected problems.

```ts
// app/actions.ts
"use server";

import { z } from "zod";

const schema = z.object({
  email: z.string().email("Invalid email"),
  password: z.string().min(8, "At least 8 characters"),
});

export type FormState = { errors?: { email?: string[]; password?: string[] }; message?: string };

export async function signup(prevState: FormState, formData: FormData): Promise<FormState> {
  const parsed = schema.safeParse({
    email: formData.get("email"),
    password: formData.get("password"),
  });

  if (!parsed.success) {
    return { errors: parsed.error.flatten().fieldErrors };
  }

  await createUser(parsed.data);
  return { message: "Account created!" };
}
```

Display the returned state with `useActionState`:

```tsx
"use client";

import { useActionState } from "react";
import { signup } from "@/app/actions";

export default function SignupForm() {
  const [state, formAction, pending] = useActionState(signup, {});

  return (
    <form action={formAction}>
      <input name="email" type="email" />
      {state.errors?.email && <p>{state.errors.email[0]}</p>}

      <input name="password" type="password" />
      {state.errors?.password && <p>{state.errors.password[0]}</p>}

      <button disabled={pending}>Sign up</button>
      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```

Guidelines:

- Return errors for **expected** failures (validation, duplicate email).
- Let **unexpected** errors throw so the nearest `error.tsx` handles them.

[Previous](./[7]-Caching-And-Revalidation.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[9]-Route-Handlers-And-APIs.md)