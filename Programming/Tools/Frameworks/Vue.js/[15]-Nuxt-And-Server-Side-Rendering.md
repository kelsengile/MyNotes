[Previous](./[14]-TypeScript-With-Vue.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[16]-Testing-Vue-Applications.md)

*Ecosystem And Delivery*

# Lesson 15 - Nuxt And Server-Side Rendering

## 15.1 What Nuxt adds

A plain Vue SPA sends an almost empty HTML page and builds everything in the browser. This can be slow on first load and harder for search engines to read. **Server-side rendering (SSR)** generates the HTML on the server first, then Vue "hydrates" it in the browser to make it interactive.

**Nuxt** is a framework built on Vue that provides SSR and many conveniences out of the box:

| Feature | Description |
|---|---|
| File-based routing | Files in `pages/` become routes automatically |
| Auto-imports | `ref`, `computed`, composables, and components need no import statements |
| Data fetching | `useFetch` and `useAsyncData` work on server and client |
| Server routes | Write backend API endpoints in the same project |
| Rendering modes | SSR, static generation, SPA, or a mix per route |
| Modules | Plug-ins for image optimization, content, auth, and more |

Create a Nuxt project:

```bash
npm create nuxt@latest my-nuxt-app
cd my-nuxt-app
npm install
npm run dev
```

Typical structure:

```
my-nuxt-app/
├── app/
│   ├── app.vue          # Root component
│   ├── pages/           # Routes
│   ├── components/      # Auto-imported components
│   ├── composables/     # Auto-imported composables
│   └── layouts/         # Page layouts
├── server/              # Server API routes
├── public/              # Static files
└── nuxt.config.ts       # Configuration
```

Nuxt's folder layout changes between major versions (older versions keep `pages/` at the project root), so check the official docs at [nuxt.com](https://nuxt.com) for your version.

---

## 15.2 File-based routing

You do not write a router config in Nuxt. Create `.vue` files inside `pages/` and routes appear automatically:

```
pages/
├── index.vue              →  /
├── about.vue              →  /about
├── blog/
│   ├── index.vue          →  /blog
│   └── [slug].vue         →  /blog/anything   (dynamic)
└── users/
    └── [id]/
        └── settings.vue   →  /users/42/settings
```

Enable pages by rendering `<NuxtPage />` in `app.vue`:

```vue
<!-- app.vue -->
<template>
  <div>
    <nav>
      <NuxtLink to="/">Home</NuxtLink>
      <NuxtLink to="/about">About</NuxtLink>
    </nav>
    <NuxtPage />
  </div>
</template>
```

Reading a dynamic parameter in `pages/blog/[slug].vue`:

```vue
<script setup>
const route = useRoute()   // auto-imported, no import needed
</script>

<template>
  <h1>Post: {{ route.params.slug }}</h1>
</template>
```

**Layouts** wrap pages with shared structure (like a header and footer):

```vue
<!-- layouts/default.vue -->
<template>
  <div>
    <header>My Site</header>
    <slot />
    <footer>© 2026</footer>
  </div>
</template>
```

**Route middleware** (in `middleware/`) works like Vue Router navigation guards.

---

## 15.3 Rendering modes

Nuxt lets you choose how pages are produced:

| Mode | How it works | Best for |
|---|---|---|
| **SSR** (universal, default) | HTML generated on the server for each request | Dynamic, SEO-sensitive pages |
| **SSG** (static generation) | HTML generated once at build time | Blogs, docs, marketing sites |
| **SPA** | Rendered only in the browser | Dashboards behind a login |
| **Hybrid** | Different modes per route | Mixed sites |

Static generation:

```bash
npx nuxt generate
```

Set modes in `nuxt.config.ts` using `routeRules`:

```ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },          // built once at build time
    '/blog/**': { swr: 3600 },         // cached, regenerated at most hourly
    '/dashboard/**': { ssr: false }    // client-side only
  }
})
```

Fetching data that works on both server and client:

```vue
<script setup>
const { data: posts, status, error } = await useFetch('https://jsonplaceholder.typicode.com/posts?_limit=5')
</script>

<template>
  <p v-if="status === 'pending'">Loading...</p>
  <p v-else-if="error">Failed to load</p>
  <ul v-else>
    <li v-for="post in posts" :key="post.id">{{ post.title }}</li>
  </ul>
</template>
```

During SSR, the data is fetched on the server and sent along with the HTML, so the browser does **not** fetch it again.

Because components run on the server too, avoid using browser-only APIs (`window`, `document`) at the top level. Use `onMounted`, or wrap content in `<ClientOnly>`:

```vue
<ClientOnly>
  <MyMapComponent />
</ClientOnly>
```

---

## 15.4 Server routes

Nuxt includes a server engine (**Nitro**) so you can write backend endpoints in the same project. Files in `server/api/` become API routes:

```ts
// server/api/hello.ts       →  GET /api/hello
export default defineEventHandler(() => {
  return { message: 'Hello from the server' }
})
```

Dynamic route with parameters, `server/api/users/[id].ts`:

```ts
export default defineEventHandler((event) => {
  const id = getRouterParam(event, 'id')
  return { id, name: `User ${id}` }
})
```

Handling different HTTP methods with file suffixes:

```ts
// server/api/todos.post.ts   →  POST /api/todos
export default defineEventHandler(async (event) => {
  const body = await readBody(event)

  if (!body.text) {
    throw createError({ statusCode: 400, statusMessage: 'Text is required' })
  }

  return { id: Date.now(), text: body.text }
})
```

Call your own API from a page:

```vue
<script setup>
const { data } = await useFetch('/api/hello')
</script>

<template>
  <p>{{ data?.message }}</p>
</template>
```

Server code can read secrets from environment variables safely, because it never reaches the browser. Define them in `runtimeConfig`:

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    apiSecret: '',            // server-only (set via NUXT_API_SECRET)
    public: { apiBase: '' }   // exposed to the browser
  }
})
```

---

[Previous](./[14]-TypeScript-With-Vue.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[16]-Testing-Vue-Applications.md)