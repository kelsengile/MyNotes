[Previous](./[2]-Components-And-Markup.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[4]-Props-And-Component-Communication.md)

*Basics*

# Lesson 3 - Reactivity With Runes

**Reactivity** means that when your data changes, the page updates automatically. In Svelte 5, reactivity is controlled by **runes**: special function-like keywords that start with `$`. They are part of the language, so you do not import them.

## 3.1 $state

`$state` declares a piece of reactive state.

```svelte
<script>
  let count = $state(0);
</script>

<button onclick={() => count++}>
  Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

Every time `count` changes, the parts of the page that use it update.

**Objects and arrays are deeply reactive.** Svelte wraps them in a proxy, so changing a nested property or using `push` works:

```svelte
<script>
  let todos = $state([{ text: 'Learn runes', done: false }]);

  function add() {
    todos.push({ text: 'New task', done: false });
  }

  function toggle(todo) {
    todo.done = !todo.done;
  }
</script>
```

Other things to know:

- `$state.raw(value)` skips the deep proxy. Use it for large data you replace entirely instead of mutating.
- `$state.snapshot(obj)` gives you a plain, non-reactive copy, useful before passing data to `structuredClone` or an external library.
- State can be class fields: `class Timer { seconds = $state(0); }`.
- Destructuring a reactive object copies the current values and loses reactivity. Read properties directly instead.

---

## 3.2 $derived

`$derived` creates a value that is **computed from other state** and stays up to date automatically.

```svelte
<script>
  let width = $state(4);
  let height = $state(5);
  let area = $derived(width * height);
</script>

<input type="number" bind:value={width} />
<input type="number" bind:value={height} />
<p>Area: {area}</p>
```

For logic that needs several statements, use `$derived.by`:

```svelte
<script>
  let numbers = $state([1, 2, 3]);

  let total = $derived.by(() => {
    let sum = 0;
    for (const n of numbers) sum += n;
    return sum;
  });
</script>
```

Rules:

- The expression must be **free of side effects**. Do not change state inside it.
- Derived values are recalculated only when their dependencies change, and only when read.
- Prefer `$derived` over copying state with an effect.

---

## 3.3 $effect

`$effect` runs code **after the DOM has updated** whenever the reactive values it reads change. Use it for work outside Svelte, such as setting the page title, drawing on a canvas, or calling a library.

```svelte
<script>
  let count = $state(0);

  $effect(() => {
    document.title = `Clicked ${count} times`;
  });
</script>
```

How it works:

- Svelte tracks every reactive value **read during the effect's run**, and re-runs the effect when any of them change.
- Effects run only in the browser, never during server rendering.
- An effect can return a **cleanup function**, which runs before the next run and when the component is destroyed (see Lesson 7).
- `$effect.pre` runs before the DOM updates, which is rarely needed.

**Avoid using `$effect` to keep state in sync.** This is a common mistake:

```js
// Avoid
let doubled = $state(0);
$effect(() => {
  doubled = count * 2;
});

// Prefer
let doubled = $derived(count * 2);
```

Rule of thumb: if you can express it as a calculation, use `$derived`. Use `$effect` only for side effects.

---

## 3.4 When runes replace older patterns

Svelte 4 and earlier used different tools. Svelte 5 still runs them (called **legacy mode**), but runes are the recommended way. A component uses either runes or legacy features, not a mix of both.

| Old pattern (Svelte 4) | Svelte 5 replacement |
|---|---|
| `let count = 0;` (implicitly reactive) | `let count = $state(0);` |
| `$: doubled = count * 2;` | `let doubled = $derived(count * 2);` |
| `$: { console.log(count); }` | `$effect(() => { console.log(count); });` |
| `export let name;` | `let { name } = $props();` |
| `on:click={fn}` | `onclick={fn}` |
| `<slot />` | `{@render children?.()}` |
| `createEventDispatcher` | Callback props |
| `beforeUpdate` / `afterUpdate` | `$effect.pre` / `$effect` |

Stores (Lesson 6) are not removed and still work. Many projects use runes for component state and stores or shared runes state for global state.

[Previous](./[2]-Components-And-Markup.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[4]-Props-And-Component-Communication.md)