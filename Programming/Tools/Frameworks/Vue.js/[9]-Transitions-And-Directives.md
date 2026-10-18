[Previous](./[8]-Forms-And-User-Input.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[10]-Vue-Router.md)

*Components*

# Lesson 9 - Transitions And Directives

## 9.1 Transition and TransitionGroup

Vue's built-in `<Transition>` component animates an element when it enters or leaves the page (for example with `v-if` or `v-show`).

```vue
<script setup>
import { ref } from 'vue'

const show = ref(true)
</script>

<template>
  <button @click="show = !show">Toggle</button>

  <Transition name="fade">
    <p v-if="show">Hello!</p>
  </Transition>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

Vue adds CSS classes at different moments. The `name` prop sets the class prefix:

| Class | Applied |
|---|---|
| `fade-enter-from` | Start state when entering, removed after one frame |
| `fade-enter-active` | Throughout the entering phase (put the `transition` here) |
| `fade-enter-to` | End state when entering |
| `fade-leave-from` | Start state when leaving |
| `fade-leave-active` | Throughout the leaving phase |
| `fade-leave-to` | End state when leaving |

`<Transition>` works with a **single** element or component. To animate **lists** use `<TransitionGroup>`, which renders each item with a transition (and can animate movement with the `-move` class):

```vue
<script setup>
import { ref } from 'vue'

const items = ref([1, 2, 3])
let next = 4

function add() {
  items.value.push(next++)
}
function remove(n) {
  items.value = items.value.filter(i => i !== n)
}
</script>

<template>
  <button @click="add">Add</button>
  <TransitionGroup name="list" tag="ul">
    <li v-for="n in items" :key="n" @click="remove(n)">Item {{ n }}</li>
  </TransitionGroup>
</template>

<style>
.list-enter-active,
.list-leave-active {
  transition: all 0.4s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
.list-move {
  transition: transform 0.4s ease;
}
</style>
```

Items in `<TransitionGroup>` must have unique `:key` values.

---

## 9.2 Custom directives

You have used built-in directives like `v-if` and `v-model`. A **custom directive** is for reusable low-level DOM behavior, such as focusing an element.

With `<script setup>`, any variable that starts with `v` can be used as a directive:

```vue
<script setup>
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus placeholder="I get focus automatically" />
</template>
```

A directive can define hooks similar to component lifecycle hooks:

| Hook | When |
|---|---|
| `created` | Before the element's attributes are applied |
| `beforeMount` / `mounted` | Before / after the element is inserted |
| `beforeUpdate` / `updated` | Before / after the element updates |
| `beforeUnmount` / `unmounted` | Before / after the element is removed |

Hooks receive `el` (the element) and `binding` (value and arguments):

```vue
<script setup>
const vHighlight = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value
  },
  updated(el, binding) {
    el.style.backgroundColor = binding.value
  }
}
</script>

<template>
  <p v-highlight="'yellow'">Highlighted text</p>
</template>
```

To use a directive across the whole app, register it in `main.js`:

```js
const app = createApp(App)
app.directive('focus', { mounted: (el) => el.focus() })
```

Use custom directives only for direct DOM access. For most reuse, components and composables are better.

---

## 9.3 Plugins

A **plugin** is code that adds app-level functionality, such as global components, directives, or shared services. A plugin is an object with an `install` method (or just a function):

```js
// src/plugins/greeting.js
export default {
  install(app, options) {
    // add a global property
    app.config.globalProperties.$greet = (name) => {
      return `${options.prefix} ${name}!`
    }

    // register a global component
    // app.component('MyComponent', MyComponent)

    // provide something to every component
    app.provide('greetingPrefix', options.prefix)
  }
}
```

Install it in `main.js` with `app.use()`:

```js
import { createApp } from 'vue'
import App from './App.vue'
import greeting from './plugins/greeting'

createApp(App)
  .use(greeting, { prefix: 'Hello,' })
  .mount('#app')
```

Use it in any component:

```vue
<script setup>
import { inject } from 'vue'

const prefix = inject('greetingPrefix')
</script>

<template>
  <p>{{ prefix }} World</p>
  <p>{{ $greet('Ada') }}</p>
</template>
```

Vue Router and Pinia are both plugins: `app.use(router)` and `app.use(pinia)`.

---

## 9.4 Teleport and Suspense

**`<Teleport>`** renders part of a component's template at a different place in the DOM, which is useful for modals, tooltips, and notifications that must not be trapped inside a parent's CSS (like `overflow: hidden`).

```vue
<script setup>
import { ref } from 'vue'

const open = ref(false)
</script>

<template>
  <button @click="open = true">Open modal</button>

  <Teleport to="body">
    <div v-if="open" class="modal">
      <p>I am rendered as a direct child of &lt;body&gt;</p>
      <button @click="open = false">Close</button>
    </div>
  </Teleport>
</template>

<style>
.modal {
  position: fixed;
  inset: 0;
  background: white;
  padding: 2rem;
}
</style>
```

The component logic stays in place. Only the generated HTML moves.

**`<Suspense>`** shows fallback content while async dependencies (such as a component using top-level `await`) are loading:

```vue
<!-- UserProfile.vue -->
<script setup>
const res = await fetch('https://jsonplaceholder.typicode.com/users/1')
const user = await res.json()
</script>

<template>
  <h2>{{ user.name }}</h2>
</template>
```

```vue
<!-- Parent.vue -->
<script setup>
import UserProfile from './UserProfile.vue'
</script>

<template>
  <Suspense>
    <UserProfile />

    <template #fallback>
      <p>Loading...</p>
    </template>
  </Suspense>
</template>
```

> `<Suspense>` is still marked **experimental** in Vue 3, so its API may change. Check the official docs before relying on it in production.

---

[Previous](./[8]-Forms-And-User-Input.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[10]-Vue-Router.md)