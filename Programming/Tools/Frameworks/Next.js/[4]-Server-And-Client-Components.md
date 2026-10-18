[Previous](./[3]-Navigation-And-Special-Files.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[5]-Data-Fetching.md)

*Basics*

# Lesson 4 - Server And Client Components

## 4.1 Server Components by default

In the App Router, every component is a **Server Component** unless you say otherwise. Server Components run only on the server (at build time or per request). Their JavaScript is **not** sent to the browser.

Benefits:

- **Smaller bundles**: heavy libraries used only on the server never reach the client.
- **Direct data access**: query a database or read secrets safely.
- **Better performance**: HTML is ready when it arrives.

Because they run on the server, Server Components can be `async`:

```tsx
// app/users/page.tsx (Server Component)
export default async function UsersPage() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const users: { id: number; name: string }[] = await res.json();

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

Server Components **cannot** use state (`useState`), effects (`useEffect`), event handlers (`onClick`), or browser APIs (`window`, `localStorage`).

---

## 4.2 The use client boundary

When you need interactivity, add the `"use client"` directive at the very top of a file. That file and everything it imports become part of the **client bundle**.

```tsx
// app/components/Counter.tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Clicked {count} times</button>;
}
```

Use a Client Component when you need:

- State and lifecycle (`useState`, `useEffect`, `useReducer`)
- Event handlers (`onClick`, `onChange`)
- Browser-only APIs (`localStorage`, `window`, geolocation)
- Custom hooks that rely on the above
- Context providers and consumers

`"use client"` marks a **boundary**, not a single component. You only need it at the entry point; components imported by that file are automatically client code. Client Components are still pre-rendered to HTML on the server first, then hydrated in the browser.

---

## 4.3 Passing data between them

**Server → Client:** pass data as props. Props must be **serializable** (strings, numbers, booleans, arrays, plain objects, Dates, Promises, and Server Actions). Functions, class instances, and event handlers cannot be passed (except Server Actions).

```tsx
// app/page.tsx (Server)
import LikeButton from "./components/LikeButton";

export default async function Page() {
  const post = await getPost(); // runs on the server
  return <LikeButton initialLikes={post.likes} />;
}
```

```tsx
// app/components/LikeButton.tsx
"use client";

import { useState } from "react";

export default function LikeButton({ initialLikes }: { initialLikes: number }) {
  const [likes, setLikes] = useState(initialLikes);
  return <button onClick={() => setLikes(likes + 1)}>{likes} likes</button>;
}
```

**Client → Server:** a Client Component cannot import a Server Component. It can, however, receive one as `children` or another prop (see 4.4). To send data back to the server, call a Server Action or an API route.

To prevent server-only code from leaking into the client, install the `server-only` package and import it at the top of sensitive modules. The build fails if a Client Component imports it.

```ts
// lib/data.ts
import "server-only";

export async function getSecretData() {
  /* uses process.env.DB_PASSWORD safely */
}
```

---

## 4.4 Composition patterns

**Keep client boundaries small.** Instead of marking a whole page `"use client"`, extract only the interactive piece.

```tsx
// Good: only SearchBar is client code
export default function Page() {
  return (
    <main>
      <h1>Products</h1>   {/* Server */}
      <SearchBar />       {/* Client */}
      <ProductList />     {/* Server */}
    </main>
  );
}
```

**Pass Server Components as children** to Client Components. They remain server-rendered:

```tsx
// app/components/Modal.tsx
"use client";

import { useState } from "react";

export default function Modal({ children }: { children: React.ReactNode }) {
  const [open, setOpen] = useState(false);
  return (
    <>
      <button onClick={() => setOpen(!open)}>Toggle</button>
      {open && <div className="modal">{children}</div>}
    </>
  );
}
```

```tsx
// app/page.tsx (Server)
import Modal from "./components/Modal";
import ServerContent from "./components/ServerContent";

export default function Page() {
  return (
    <Modal>
      <ServerContent />
    </Modal>
  );
}
```

**Context providers** need `"use client"`. Create a provider component and wrap `children` with it in the layout, placing it as deep in the tree as possible:

```tsx
// app/providers.tsx
"use client";

import { createContext } from "react";

export const ThemeContext = createContext("light");

export default function Providers({ children }: { children: React.ReactNode }) {
  return <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>;
}
```

```tsx
// app/layout.tsx
import Providers from "./providers";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

[Previous](./[3]-Navigation-And-Special-Files.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[5]-Data-Fetching.md)