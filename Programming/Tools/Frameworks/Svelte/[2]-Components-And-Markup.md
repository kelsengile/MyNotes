[Previous](./[1]-What-Is-Svelte.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[3]-Reactivity-With-Runes.md)

*Basics*

# Lesson 2 - Components And Markup

## 2.1 Expressions in markup

Use curly braces to put JavaScript values into your markup.

```svelte
<script>
  let name = 'Ada';
  let price = 4;
  let quantity = 3;
  let imageUrl = '/logo.png';
</script>

<!-- In text -->
<p>Hello {name.toUpperCase()}!</p>
<p>Total: ${price * quantity}</p>

<!-- In attributes -->
<img src={imageUrl} alt="Our logo" />

<!-- Shorthand when the attribute and variable names match -->
<img src={imageUrl} {alt} />
```

Things to know:

- **Attributes** accept expressions: `src={imageUrl}`. Do not wrap them in quotes unless you want a string.
- **Shorthand**: `{name}` is the same as `name={name}`.
- **Spreading**: `<Button {...props} />` passes every key of an object as an attribute or prop.
- **Event attributes** are normal attributes named after the event: `onclick={handler}`.
- **Conditional classes**: `class:active={isActive}` adds the class `active` when `isActive` is true.
- **Raw HTML**: `{@html htmlString}` inserts unescaped HTML. Only use it with content you trust, because it can open the door to cross-site scripting (XSS) attacks.

**Using other components.** Import the file and use it like a tag. Component names must start with a capital letter.

```svelte
<script>
  import Button from './Button.svelte';
</script>

<Button />
```

---

## 2.2 if, each, and await blocks

Blocks start with `{#`, continue with `{:`, and end with `{/`.

**`{#if}`** shows content conditionally:

```svelte
{#if score >= 90}
  <p>Excellent!</p>
{:else if score >= 50}
  <p>Passed</p>
{:else}
  <p>Try again</p>
{/if}
```

**`{#each}`** repeats content for each item of an array (or any array-like value):

```svelte
<script>
  let todos = [
    { id: 1, text: 'Learn Svelte' },
    { id: 2, text: 'Build an app' }
  ];
</script>

<ul>
  {#each todos as todo, i (todo.id)}
    <li>{i + 1}. {todo.text}</li>
  {:else}
    <li>Nothing to do</li>
  {/each}
</ul>
```

- `i` is the optional index.
- `(todo.id)` is the **key**. It tells Svelte which item is which, so it can add, remove, and reorder items correctly. Use a key whenever items can change.
- `{:else}` renders when the list is empty.

**`{#await}`** handles a promise in three states:

```svelte
<script>
  async function getUser() {
    const res = await fetch('https://jsonplaceholder.typicode.com/users/1');
    if (!res.ok) throw new Error('Request failed');
    return res.json();
  }

  let promise = getUser();
</script>

{#await promise}
  <p>Loading...</p>
{:then user}
  <p>Hello {user.name}</p>
{:catch error}
  <p>Something went wrong: {error.message}</p>
{/await}
```

**`{#key}`** destroys and recreates its content whenever the given value changes. This is handy for replaying a transition:

```svelte
{#key selectedId}
  <Details id={selectedId} />
{/key}
```

---

## 2.3 Snippets

A **snippet** is a named, reusable chunk of markup inside a component. It can take parameters. You declare it with `{#snippet}` and use it with `{@render}`.

```svelte
<script>
  let items = [
    { id: 1, name: 'Tea', price: 3 },
    { id: 2, name: 'Coffee', price: 4 }
  ];
</script>

{#snippet row(item)}
  <li>{item.name} - ${item.price}</li>
{/snippet}

<ul>
  {#each items as item (item.id)}
    {@render row(item)}
  {/each}
</ul>
```

Rules and tips:

- Snippets can see variables declared in the same component, including the `<script>` block.
- Snippets can be passed to other components as props, which is how Svelte 5 handles customizable content (see Lesson 4).
- Snippets replace the older `<slot>` feature.

---

## 2.4 Component styles scoped by default

CSS in a `<style>` block applies **only to that component**. Svelte adds a unique class (like `svelte-abc123`) to your elements and selectors so styles cannot leak out.

```svelte
<p>This paragraph is purple.</p>

<style>
  p {
    color: purple;
  }
</style>
```

Another component's `<p>` elements are not affected.

Useful details:

- **Unused selectors** produce a compile warning and are removed from the output, which helps you spot dead CSS.
- **`:global(...)`** escapes scoping when you need to style elements you do not control, such as HTML rendered with `{@html}`:

```svelte
<style>
  .article :global(a) {
    color: teal;
  }
</style>
```

- **CSS variables** let a parent pass values to a child's styles:

```svelte
<!-- Parent -->
<Card --accent="tomato" />

<!-- Card.svelte -->
<div class="card">Hello</div>

<style>
  .card {
    border: 2px solid var(--accent, gray);
  }
</style>
```

[Previous](./[1]-What-Is-Svelte.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[3]-Reactivity-With-Runes.md)