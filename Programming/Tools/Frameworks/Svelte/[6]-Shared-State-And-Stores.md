[Previous](./[5]-Bindings-And-Events.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[7]-Lifecycle-And-Effects.md)

*Components In Depth*

# Lesson 6 - Shared State And Stores

State declared inside a component belongs to that component. When several unrelated components need the same data (a logged-in user, a shopping cart, a theme), you need **shared state**. Svelte offers two approaches: **stores** (the classic way) and **shared reactive state with runes**.

## 6.1 Writable and readable stores

A **store** is an object with a `subscribe` method. Anyone who subscribes is told whenever the value changes.

**Writable stores** can be changed from anywhere:

```js
// src/lib/counter.js
import { writable } from 'svelte/store';

export const count = writable(0);
```

```js
import { count, } from '$lib/counter.js';
import { get } from 'svelte/store';

count.set(5);                     // replace the value
count.update((n) => n + 1);       // compute from the old value
console.log(get(count));          // read once: 6

const unsubscribe = count.subscribe((value) => {
  console.log('count is now', value);
});
unsubscribe();                    // stop listening
```

**Readable stores** can only be changed from inside. They suit values that come from outside, like the time:

```js
import { readable } from 'svelte/store';

export const time = readable(new Date(), (set) => {
  const interval = setInterval(() => set(new Date()), 1000);

  // Cleanup: runs when the last subscriber leaves
  return () => clearInterval(interval);
});
```

The second argument runs when the first subscriber arrives and returns a cleanup function for when the last one leaves.

---

## 6.2 Derived stores

A **derived store** is computed from one or more other stores.

```js
import { writable, derived } from 'svelte/store';

export const price = writable(10);
export const quantity = writable(3);

// One source
export const doubledPrice = derived(price, ($price) => $price * 2);

// Several sources
export const total = derived(
  [price, quantity],
  ([$price, $quantity]) => $price * $quantity
);
```

The `$` prefix on the callback parameters is only a naming habit meaning "the current value of this store". Derived stores update automatically when any source changes.

---

## 6.3 The $ store syntax

Inside a component, prefix a store's name with `$` to **read its value and subscribe automatically**. Svelte unsubscribes for you when the component is destroyed.

```svelte
<script>
  import { count } from '$lib/counter.js';
</script>

<p>The count is {$count}</p>

<button onclick={() => $count++}>Increment</button>
<button onclick={() => ($count = 0)}>Reset</button>
```

Rules:

- `$count` is only valid for stores declared at the **top level** of a component's `<script>` (including imports).
- Assigning to `$count` calls `count.set(...)` for writable stores.
- Names beginning with `$` are reserved for this feature, so do not name your own variables that way.
- You can write your own store by returning an object with a `subscribe` function (and optionally `set`), which is useful for wrapping persistence or custom logic.

---

## 6.4 Shared reactive state with runes

Runes also work outside components, in files ending in **`.svelte.js`** or **`.svelte.ts`**. This gives you shared state without the store API.

```js
// src/lib/counter.svelte.js
export const counter = $state({ count: 0 });

export function increment() {
  counter.count += 1;
}
```

```svelte
<script>
  import { counter, increment } from '$lib/counter.svelte.js';
</script>

<button onclick={increment}>{counter.count}</button>
```

An important restriction: you **cannot export a `$state` variable that you reassign** (`let count = $state(0); count = 1`), because importers could not see the change. Instead, export an object and change its properties, or export functions or getters.

A class is a neat way to bundle state, derived values, and actions:

```js
// src/lib/cart.svelte.js
class Cart {
  items = $state([]);
  total = $derived(this.items.reduce((sum, item) => sum + item.price, 0));

  add(item) {
    this.items.push(item);
  }

  clear() {
    this.items = [];
  }
}

export const cart = new Cart();
```

```svelte
<script>
  import { cart } from '$lib/cart.svelte.js';
</script>

<p>{cart.items.length} items, total ${cart.total}</p>
<button onclick={() => cart.add({ name: 'Tea', price: 3 })}>Add tea</button>
```

**Which should you choose?**

| Situation | Suggestion |
|---|---|
| New code in a runes project | Shared runes state |
| Existing code or libraries that expect stores | Stores |
| Streams of values with setup and cleanup (time, sockets) | Stores (`readable`) or effects |

**Server warning:** on the server, module-level state is shared by every visitor's request. Never store user-specific data in a module-level variable during server rendering. Use context (Lesson 4) or `event.locals` (Lesson 11) instead.

[Previous](./[5]-Bindings-And-Events.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[7]-Lifecycle-And-Effects.md)