[Previous](./[9]-Transitions-And-Directives.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[11]-State-Management-With-Pinia.md)

*Building Applications*

# Lesson 10 - Vue Router

## 10.1 Configuring routes

A **single-page application (SPA)** loads one HTML page and swaps components as the URL changes, without full page reloads. **Vue Router** is the official routing library.

If you did not select Vue Router in `create-vue`, install it:

```bash
npm install vue-router@4
```

Create `src/router/index.js`:

```js
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'
import AboutView from '../views/AboutView.vue'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', name: 'home', component: HomeView },
    { path: '/about', name: 'about', component: AboutView }
  ]
})

export default router
```

Register it in `main.js`:

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App).use(router).mount('#app')
```

Use `<RouterLink>` for navigation and `<RouterView>` to display the matching component, in `App.vue`:

```vue
<template>
  <nav>
    <RouterLink to="/">Home</RouterLink> |
    <RouterLink to="/about">About</RouterLink>
  </nav>

  <RouterView />   <!-- the matched route's component appears here -->
</template>
```

`<RouterLink>` renders an `<a>` tag but prevents a full page reload. It also adds an `router-link-active` CSS class to the current link.

Navigate from code using `useRouter`:

```vue
<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

function goHome() {
  router.push('/')              // or router.push({ name: 'home' })
}
</script>
```

Two history modes exist: `createWebHistory()` gives clean URLs (`/about`) but needs server configuration, while `createWebHashHistory()` uses `#/about` and works anywhere.

---

## 10.2 Dynamic and nested routes

**Dynamic routes** use a `:param` segment to match changing values:

```js
{ path: '/users/:id', name: 'user', component: UserView }
```

`/users/1`, `/users/42`, and `/users/ada` all match. Read the value with `useRoute`:

```vue
<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
</script>

<template>
  <h1>User {{ route.params.id }}</h1>
</template>
```

Link to it:

```vue
<RouterLink :to="{ name: 'user', params: { id: 42 } }">User 42</RouterLink>
```

When only the param changes (`/users/1` → `/users/2`), the same component instance is **reused**, so its `onMounted` does not run again. Use a watcher to react:

```js
import { watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
watch(() => route.params.id, (newId) => {
  console.log('Load user', newId)
})
```

Query strings are available in `route.query` (`/search?q=vue` gives `route.query.q`).

**Nested routes** let a parent route contain its own `<RouterView>`:

```js
{
  path: '/dashboard',
  component: DashboardLayout,
  children: [
    { path: '', component: DashboardHome },          // /dashboard
    { path: 'settings', component: SettingsView },    // /dashboard/settings
    { path: 'profile', component: ProfileView }       // /dashboard/profile
  ]
}
```

```vue
<!-- DashboardLayout.vue -->
<template>
  <aside>Sidebar</aside>
  <RouterView />   <!-- child routes appear here -->
</template>
```

Add a catch-all route last to handle unknown URLs:

```js
{ path: '/:pathMatch(.*)*', name: 'not-found', component: NotFoundView }
```

---

## 10.3 Navigation guards

**Navigation guards** run before or after navigation and can allow, redirect, or cancel it. A common use is protecting pages that require login.

**Global guard:**

```js
router.beforeEach((to, from) => {
  const isLoggedIn = Boolean(localStorage.getItem('token'))

  if (to.meta.requiresAuth && !isLoggedIn) {
    return { name: 'login', query: { redirect: to.fullPath } }   // redirect
  }
  // returning nothing (or true) allows the navigation
})
```

`meta` is custom data attached to a route:

```js
{ path: '/account', component: AccountView, meta: { requiresAuth: true } }
```

**Per-route guard:**

```js
{
  path: '/admin',
  component: AdminView,
  beforeEnter: (to, from) => {
    if (!isAdmin()) return '/'
  }
}
```

**In-component guards** with the Composition API:

```vue
<script setup>
import { onBeforeRouteLeave } from 'vue-router'

onBeforeRouteLeave(() => {
  const answer = window.confirm('Discard unsaved changes?')
  if (!answer) return false   // cancel navigation
})
</script>
```

A guard can return:

| Return value | Result |
|---|---|
| nothing / `true` | Allow |
| `false` | Cancel |
| a route location (`'/login'` or an object) | Redirect |

Use client-side guards for user experience only. **Real security must be enforced on the server**, because anyone can bypass the browser's JavaScript.

---

## 10.4 Lazy-loaded routes

By default, every view is bundled into one JavaScript file, so users download code for pages they may never visit. **Lazy loading** splits each page into its own file that loads only when needed.

Replace the static import with a dynamic `import()`:

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'home',
      component: () => import('../views/HomeView.vue')
    },
    {
      path: '/about',
      name: 'about',
      // loaded only when the user visits /about
      component: () => import('../views/AboutView.vue')
    }
  ]
})
```

Vite automatically creates a separate **chunk** file for each dynamic import. You can verify this by running `npm run build` and looking at the `dist/assets/` folder.

Tips:

- Keep the home page statically imported for the fastest first load, and lazy-load less-visited pages.
- Use `<Suspense>` or a loading indicator if a lazy view loads slowly.
- Use `defineAsyncComponent` (Lesson 17) to lazy-load regular components, not only routes.

---

[Previous](./[9]-Transitions-And-Directives.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[11]-State-Management-With-Pinia.md)