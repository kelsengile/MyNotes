[Previous](./[3]-Reactivity-With-Runes.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[5]-Bindings-And-Events.md)

*Components In Depth*

# Lesson 4 - Props And Component Communication

Components are most useful when they can be configured by their parent and report back to it. **Props** pass data down. **Callback props** send messages up. **Context** shares data with deeply nested descendants.

## 4.1 Declaring props with $props

A child component receives props through `$props()`, using normal destructuring.

```svelte
<!-- Greeting.svelte -->
<script>
  let { name, greeting = 'Hello' } = $props();
</script>

<p>{greeting}, {name}!</p>
```

```svelte
<!-- Parent -->
<script>
  import Greeting from './Greeting.svelte';
</script>

<Greeting name="Ada" />
<Greeting name="Linus" greeting="Welcome" />
```

Useful patterns:

```js
// Default values
let { size = 'medium' } = $props();

// Rename a prop (needed for reserved words like "class")
let { class: className } = $props();

// Collect the rest of the props to forward them
let { label, ...rest } = $props();
```

```svelte
<button {...rest}>{label}</button>
```

Important rules:

- Props flow **down**. Do not reassign a prop in the child. If the child must change a value, use a callback prop or a **bindable** prop.
- A prop can be made two-way with `$bindable`:

```svelte
<!-- TextField.svelte -->
<script>
  let { value = $bindable('') } = $props();
</script>

<input bind:value />
```

```svelte
<!-- Parent -->
<TextField bind:value={username} />
```

---

## 4.2 Callback props

To tell a parent that something happened, pass a **function as a prop**. The child calls it.

```svelte
<!-- ColorPicker.svelte -->
<script>
  let { colors, onselect } = $props();
</script>

{#each colors as color (color)}
  <button onclick={() => onselect(color)}>{color}</button>
{/each}
```

```svelte
<!-- Parent -->
<script>
  import ColorPicker from './ColorPicker.svelte';

  let chosen = $state('none');
</script>

<ColorPicker colors={['red', 'green', 'blue']} onselect={(c) => (chosen = c)} />
<p>You picked: {chosen}</p>
```

Naming convention: start callback props with `on`, for example `onselect`, `onclose`, `onsubmit`. This replaces the older `createEventDispatcher` approach.

---

## 4.3 Children and snippets

Anything you put between a component's tags becomes the `children` snippet. The component shows it with `{@render}`.

```svelte
<!-- Card.svelte -->
<script>
  let { title, children } = $props();
</script>

<section class="card">
  <h2>{title}</h2>
  {@render children?.()}
</section>
```

```svelte
<!-- Parent -->
<Card title="Profile">
  <p>This text is passed as children.</p>
</Card>
```

The `?.` makes the content optional, so the card still works when no children are given.

You can pass **additional named snippets** as props, and snippets can take parameters:

```svelte
<!-- List.svelte -->
<script>
  let { items, row, empty } = $props();
</script>

{#if items.length}
  <ul>
    {#each items as item (item.id)}
      <li>{@render row(item)}</li>
    {/each}
  </ul>
{:else}
  {@render empty?.()}
{/if}
```

```svelte
<!-- Parent -->
<List {items}>
  {#snippet row(item)}
    <strong>{item.name}</strong>
  {/snippet}
  {#snippet empty()}
    <p>No items yet.</p>
  {/snippet}
</List>
```

Snippets declared directly inside a component tag are automatically passed as props with the same name.

---

## 4.4 Context

When data is needed deep in the tree, passing props through every level is tedious (this is called "prop drilling"). **Context** lets a parent make a value available to all of its descendants.

```svelte
<!-- ThemeProvider.svelte -->
<script>
  import { setContext } from 'svelte';

  let { children } = $props();

  const theme = $state({ mode: 'light' });
  setContext('theme', theme);
</script>

<button onclick={() => (theme.mode = theme.mode === 'light' ? 'dark' : 'light')}>
  Toggle theme
</button>

{@render children()}
```

```svelte
<!-- Any descendant, however deep -->
<script>
  import { getContext } from 'svelte';

  const theme = getContext('theme');
</script>

<p>Current mode: {theme.mode}</p>
```

Rules:

- `setContext` and `getContext` must be called **while the component is first initializing** (during `<script>`), not later inside event handlers.
- Context is **not reactive by itself**. To keep it reactive, store a `$state` object (as above) and change its properties, rather than putting a plain value in context.
- Context is scoped to a component subtree, so two separate trees can have different values, which makes it safer than a global variable for server rendering.
- Use a string, or a unique `Symbol`, as the key. A `Symbol` avoids name clashes.

[Previous](./[3]-Reactivity-With-Runes.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[5]-Bindings-And-Events.md)