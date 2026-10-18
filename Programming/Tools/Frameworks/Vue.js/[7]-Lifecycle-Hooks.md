[Previous](./[6]-Composition-API-And-Composables.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[8]-Forms-And-User-Input.md)

*Components*

# Lesson 7 - Lifecycle Hooks

## 7.1 onMounted and onUnmounted

Every component goes through stages: it is **created**, **mounted** (added to the page), **updated**, and **unmounted** (removed). **Lifecycle hooks** let you run code at these moments.

```
Setup runs ─► onBeforeMount ─► onMounted ─► (updates) ─► onBeforeUnmount ─► onUnmounted
```

**`onMounted`** runs after the component's DOM has been created and inserted. It is the place for:

- Fetching initial data
- Accessing DOM elements
- Starting timers or subscriptions

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const width = ref(window.innerWidth)

function updateWidth() {
  width.value = window.innerWidth
}

onMounted(() => {
  console.log('Component is on the page')
  window.addEventListener('resize', updateWidth)
})

onUnmounted(() => {
  console.log('Component removed')
  window.removeEventListener('resize', updateWidth)
})
</script>

<template>
  <p>Window width: {{ width }}px</p>
</template>
```

**`onUnmounted`** runs after the component is removed. Use it to clean up anything you started in `onMounted`.

Setup code in `<script setup>` already acts as the "created" stage. There is no `onCreated` hook in the Composition API.

---

## 7.2 Update hooks

When reactive data changes, Vue re-renders the component. Two hooks surround that update:

| Hook | When it runs |
|---|---|
| `onBeforeUpdate` | Just before the DOM is patched |
| `onUpdated` | Just after the DOM has been patched |

```vue
<script setup>
import { ref, onBeforeUpdate, onUpdated } from 'vue'

const count = ref(0)

onBeforeUpdate(() => {
  console.log('About to update. DOM still shows the old value.')
})

onUpdated(() => {
  console.log('DOM now shows the new value.')
})
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

Important rules:

- **Do not change reactive state inside `onUpdated`.** It can cause an infinite update loop.
- To react to a specific data change, use a **watcher** instead (Lesson 3). It is more precise.
- To run code after the DOM updates following a change, use `await nextTick()`.

Other hooks you will encounter:

| Hook | Purpose |
|---|---|
| `onBeforeMount` | Before first render |
| `onBeforeUnmount` | Just before removal, while the component still works |
| `onErrorCaptured` | Catch errors from descendant components |
| `onActivated` / `onDeactivated` | For components wrapped in `<KeepAlive>` |

---

## 7.3 Cleanup patterns

Anything that keeps running after a component is gone causes **memory leaks** and bugs. Always pair "start" with "stop".

**Timers:**

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const seconds = ref(0)
let timerId

onMounted(() => {
  timerId = setInterval(() => seconds.value++, 1000)
})

onUnmounted(() => {
  clearInterval(timerId)
})
</script>

<template>
  <p>Seconds: {{ seconds }}</p>
</template>
```

**Event listeners** must be removed with the same function reference used to add them (see 7.1).

**Watcher cleanup.** Watchers receive an `onCleanup` function, useful for cancelling outdated requests:

```js
import { ref, watch } from 'vue'

const userId = ref(1)

watch(userId, async (id, oldId, onCleanup) => {
  const controller = new AbortController()
  onCleanup(() => controller.abort())   // runs before the next run or on unmount

  const res = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
    signal: controller.signal
  })
  console.log(await res.json())
})
```

Watchers and computed properties created in `<script setup>` are stopped automatically when the component unmounts.

---

## 7.4 Template refs

A **template ref** gives you direct access to a DOM element or child component instance. Add a `ref` attribute in the template and a matching `ref` variable in the script:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const inputEl = ref(null)   // same name as the ref attribute

onMounted(() => {
  inputEl.value.focus()     // the element exists only after mounting
})
</script>

<template>
  <input ref="inputEl" />
</template>
```

Key points:

- The ref is `null` until the component mounts, so access it in `onMounted` or later, never during setup.
- In Vue 3.5+, `useTemplateRef('inputEl')` is available as a clearer alternative:

```js
import { useTemplateRef, onMounted } from 'vue'

const input = useTemplateRef('inputEl')
onMounted(() => input.value.focus())
```

- On a child component, the ref gives that component's instance. With `<script setup>`, only things exposed using `defineExpose` are accessible.
- Inside `v-for`, the ref holds an **array** of elements:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const items = ref(['a', 'b', 'c'])
const itemEls = ref([])

onMounted(() => console.log(itemEls.value.length))   // 3
</script>

<template>
  <li v-for="item in items" :key="item" ref="itemEls">{{ item }}</li>
</template>
```

Use template refs only when you truly need direct DOM access (focus, measuring size, third-party libraries). For everything else, prefer data-driven templates.

---

[Previous](./[6]-Composition-API-And-Composables.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[8]-Forms-And-User-Input.md)