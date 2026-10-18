[Previous](./[17]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-React.md)

*Going Further*

# Lesson 18 - Next Steps With React

## 18.1 Next.js and meta-frameworks

A plain Vite React app renders entirely **in the browser** (client-side rendering). This is great for dashboards and internal tools, but has limits: users see a blank page until JavaScript loads, search engines get less content, and you must build the routing and data loading yourself.

A **meta-framework** is a framework built on top of React that adds routing, data loading, server rendering, and build tooling in one package.

### Rendering strategies

| Strategy | What it means | Good for |
|---|---|---|
| **CSR** (client-side rendering) | The browser builds the page with JavaScript | Apps behind a login, dashboards |
| **SSR** (server-side rendering) | The server generates HTML per request | Personalized or frequently changing pages, SEO |
| **SSG** (static site generation) | HTML is generated once at build time | Blogs, docs, marketing pages |
| **Hydration** | React attaches interactivity to server-generated HTML | Used with SSR and SSG |

### Next.js

**Next.js** (by Vercel) is the most widely used React framework. Its **App Router** is built around React Server Components ([Lesson 14](./[14]-Modern-React-Features.md)). Key features:

- **File-based routing:** folders in `app/` become URLs.
- **Layouts, loading states, and error boundaries** by file convention (`layout.js`, `loading.js`, `error.js`).
- **Server and Client Components**, and Server Functions.
- Built-in **image, font, and script optimization**.
- **API route handlers** in the same project.
- Flexible deployment, with Vercel as the most integrated host.

Create a project:

```bash
npx create-next-app@latest
```

A minimal page is a file at `app/about/page.js`:

```jsx
export default function AboutPage() {
  return <h1>About us</h1>;
}
```

That file automatically serves `/about`.

### Other options

- **React Router (framework mode):** the v7 release merged Remix's ideas into React Router, giving it loaders, actions, and server rendering. A natural step up if you already know React Router ([Lesson 9](./[9]-Routing-With-React-Router.md)).
- **TanStack Start:** a newer full-stack framework from the TanStack team.
- **Astro:** content-focused sites that can use React for interactive "islands".
- **Gatsby:** older static-site framework, much less common now.

### Which should you choose?

- **Internal tool or app behind a login with no SEO needs:** Vite + React is fine.
- **Public, content-heavy, or SEO-sensitive site:** a meta-framework (Next.js or React Router framework mode).
- **Unsure:** official React guidance generally recommends a framework for new full-stack production apps. Check react.dev for current recommendations.

---

## 18.2 React Native for mobile

**React Native** lets you build **native iOS and Android apps** using React's ideas: components, props, state, and hooks. Instead of HTML elements, you use native building blocks that render as real platform UI.

| Web (React) | Mobile (React Native) |
|---|---|
| `<div>` | `<View>` |
| `<p>`, `<span>` | `<Text>` |
| `<img>` | `<Image>` |
| `<input>` | `<TextInput>` |
| `<button>` | `<Pressable>` / `<Button>` |
| CSS | `StyleSheet` (CSS-like JavaScript objects) |

### Example

```jsx
import { useState } from "react";
import { View, Text, Pressable, StyleSheet } from "react-native";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Count: {count}</Text>
      <Pressable onPress={() => setCount(count + 1)} style={styles.button}>
        <Text style={styles.buttonText}>Increment</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, alignItems: "center", justifyContent: "center" },
  text: { fontSize: 24, marginBottom: 12 },
  button: { backgroundColor: "royalblue", padding: 12, borderRadius: 8 },
  buttonText: { color: "white" },
});
```

The structure should look familiar: `useState`, event props (note `onPress` instead of `onClick`), and JSX.

### Getting started with Expo

**Expo** is the recommended way to start a React Native project. It handles the complicated native tooling:

```bash
npx create-expo-app@latest
```

Then run the project and preview it on your phone using the **Expo Go** app, or in a simulator.

### What transfers from web React

- Components, props, state, effects, context, and custom hooks work the same way.
- Data libraries such as TanStack Query and state libraries like Zustand work too.

### What is different

- No DOM and no CSS files; layout uses **Flexbox** through style objects.
- Navigation uses different libraries (**Expo Router** or **React Navigation**).
- You must think about device features: permissions, gestures, offline use, app stores.

---

## 18.3 State and data libraries

As your apps grow, the ecosystem offers mature tools. Here is a map of where to go next, building on earlier lessons.

### State management

| Library | Idea | See |
|---|---|---|
| **Context** | Built-in, for rarely changing values | [Lesson 8](./[8]-Context-And-Global-State.md) |
| **Zustand** | Tiny store, no provider | [Lesson 8](./[8]-Context-And-Global-State.md) |
| **Redux Toolkit** | Structured, predictable store with great dev tools | [Lesson 8](./[8]-Context-And-Global-State.md) |
| **Jotai** | Small "atoms" of state that compose | Explore |
| **XState** | State machines for complex flows | Explore |

### Server data

| Library | Idea |
|---|---|
| **TanStack Query** | Fetching, caching, and syncing server state ([Lesson 10](./[10]-Data-Fetching-And-APIs.md)) |
| **SWR** | A lighter alternative with a similar approach |
| **RTK Query** | Data fetching built into Redux Toolkit |
| **Apollo Client / urql** | Clients for GraphQL APIs |
| **tRPC** | End-to-end typed APIs when your server is also TypeScript |

### Forms and validation

- **React Hook Form** for efficient form handling.
- **Zod** (or Valibot) for schemas that validate data and generate TypeScript types.

### Tables, charts, and more

- **TanStack Table** for headless, powerful data tables.
- **Recharts**, **Visx**, or **Chart.js** for charts.
- **Framer Motion** (now published as **Motion**) for animation.
- **date-fns** or **Day.js** for dates.
- **i18next** or **FormatJS** for internationalization.

### Backend options

- Build your own API with **Node.js** (Express, Fastify, NestJS) or any language.
- Use a backend-as-a-service such as **Supabase** or **Firebase** for databases, authentication, and storage.

### Advice on choosing libraries

- Add a library **when you feel the pain** it solves, not before.
- Check maintenance activity, documentation quality, bundle size, and compatibility with your React version.
- Learn the **fundamentals** first: libraries change, but the core ideas of components, state, and effects remain.

---

## 18.4 Building portfolio projects

The best way to solidify React is to build and ship real projects. A portfolio of 3 to 5 polished projects demonstrates your skills far better than a list of tutorials completed.

### Project ideas by level

**Beginner (components, props, state)**

- **Todo list** with add, complete, delete, and filter.
- **Tip calculator** or unit converter.
- **Quiz app** with scoring and a results screen.
- **Memory card game.**

**Intermediate (effects, routing, APIs)**

- **Weather dashboard** using a public weather API, with search and saved cities.
- **Movie or recipe browser** with search, filters, detail pages, and favorites.
- **Expense tracker** with charts and data stored in `localStorage`.
- **Blog** with routes, markdown content, and comments.

**Advanced (state libraries, auth, performance, testing)**

- **E-commerce storefront** with a cart, checkout flow, and authentication.
- **Kanban board** with drag-and-drop and persistence.
- **Real-time chat app** using WebSockets or a service like Supabase.
- **Full-stack app in Next.js** with a database, authentication, and server actions.

### What makes a project portfolio-ready

1. **It's deployed** at a live URL ([Lesson 17](./[17]-Building-And-Deployment.md)).
2. **It solves a real problem** or shows a clear feature set.
3. **It handles loading, error, and empty states.**
4. **It's responsive** and works on phones.
5. **It's accessible:** keyboard navigation, labels, contrast ([Lesson 16](./[16]-Accessibility-And-Best-Practices.md)).
6. **It has tests** for important flows ([Lesson 15](./[15]-Testing-React-Apps.md)).
7. **The code is clean,** with sensible structure and a consistent style ([Lesson 12](./[12]-Project-Structure-And-Tooling.md)).
8. **The README explains it:** what it does, screenshots, the tech stack, how to run it, and what you learned.

### Habits that accelerate learning

- **Read other people's code** on GitHub and in open-source projects.
- **Read the official docs** at [react.dev](https://react.dev), which are interactive and well written.
- **Contribute to open source** with small fixes or documentation improvements.
- **Use TypeScript** in at least one project, as most professional React work uses it.
- **Keep shipping.** Build, deploy, get feedback, improve.

### Where to go from here

You now have the full path: from your first component to hooks, state, routing, data, performance, modern features, testing, accessibility, and deployment. Pick a project from the list above, start small, and keep building. Good luck!

[Previous](./[17]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-React.md)