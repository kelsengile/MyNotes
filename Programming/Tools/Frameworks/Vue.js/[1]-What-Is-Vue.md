[Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[2]-Template-Syntax.md)

*Basics*

# Lesson 1 - What Is Vue

## 1.1 The progressive framework idea

Vue is called a **progressive framework** because you can adopt as much or as little of it as you need:

- **Small:** add one `<script>` tag to an existing HTML page and enhance a single widget.
- **Medium:** build components using a build tool (Vite).
- **Large:** add routing (Vue Router), shared state (Pinia), and server-side rendering (Nuxt).

You never have to adopt the whole ecosystem up front. Here is a complete working Vue app in one HTML file:

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="app">{{ message }}</div>

    <script src="https://unpkg.com/vue@3"></script>
    <script>
      const { createApp, ref } = Vue

      createApp({
        setup() {
          const message = ref('Hello Vue!')
          return { message }
        }
      }).mount('#app')
    </script>
  </body>
</html>
```

Open this file in a browser and you will see `Hello Vue!`. Vue replaced `{{ message }}` with the value of the `message` variable.

---

## 1.2 Options API vs Composition API

Vue offers two styles for writing component logic. Both do the same job and can be mixed in one project.

**Options API**: logic is organized by option type (`data`, `methods`, `computed`):

```vue
<script>
export default {
  data() {
    return { count: 0 }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>
```

**Composition API**: logic is written as plain functions and variables inside `<script setup>`:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>
```

| | Options API | Composition API |
|---|---|---|
| Organization | By option type | By feature / logic |
| Reuse of logic | Mixins (harder) | Composables (easy) |
| TypeScript support | Good | Excellent |
| Learning curve | Gentler for beginners | Slightly steeper, more flexible |

This Topic mainly uses the Composition API with `<script setup>`, the style the Vue team recommends for new projects.

---

## 1.3 Creating a project with create-vue

`create-vue` is the official project scaffolding tool. It uses **Vite** as the build tool. You need [Node.js](https://nodejs.org) installed (use a current LTS version).

Run this in a terminal:

```bash
npm create vue@latest
```

The tool asks you questions. Typical answers for learning:

| Prompt | Suggested answer |
|---|---|
| Project name | `my-vue-app` |
| TypeScript | No (for now) |
| JSX Support | No |
| Vue Router | No (covered in Lesson 10) |
| Pinia | No (covered in Lesson 11) |
| Vitest | No (covered in Lesson 16) |
| ESLint / Prettier | Optional |

Then install dependencies and start the development server:

```bash
cd my-vue-app
npm install
npm run dev
```

Vite prints a local address (usually `http://localhost:5173`). Open it in your browser. Edits to your files appear instantly thanks to **Hot Module Replacement (HMR)**.

A freshly created project looks like this:

```
my-vue-app/
├── index.html          # The single HTML page
├── package.json        # Dependencies and scripts
├── vite.config.js      # Vite configuration
├── public/             # Static files copied as-is
└── src/
    ├── main.js         # App entry point
    ├── App.vue         # Root component
    ├── assets/         # Images, CSS
    └── components/     # Your components
```

---

## 1.4 Anatomy of a single-file component

A **single-file component (SFC)** is a `.vue` file that keeps a component's logic, template, and styles together. It has three blocks:

```vue
<script setup>
import { ref } from 'vue'

const name = ref('Vue')
</script>

<template>
  <h1>Hello, {{ name }}!</h1>
  <input v-model="name" />
</template>

<style scoped>
h1 {
  color: seagreen;
}
</style>
```

| Block | Purpose |
|---|---|
| `<script setup>` | JavaScript logic: variables, functions, imports |
| `<template>` | HTML-like markup that displays the data |
| `<style>` | CSS for this component (`scoped` limits it to this component) |

Variables declared in `<script setup>` are automatically available in the `<template>`.

The app starts in `src/main.js`:

```js
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

This creates an app from the root component `App.vue` and mounts it onto the `<div id="app">` in `index.html`.

---

[Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[2]-Template-Syntax.md)