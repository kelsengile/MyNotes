[Previous](./[6]-Shared-State-And-Stores.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[8]-Transitions-And-Animations.md)

*Components In Depth*

# Lesson 7 - Lifecycle And Effects

A component has a **lifecycle**: it is created, added to the page (mounted), updated as state changes, and finally removed (destroyed). Svelte lets you run code at these moments.

## 7.1 onMount and onDestroy

```svelte
<script>
  import { onMount, onDestroy } from 'svelte';

  let posts = $state([]);

  onMount(async () => {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=5');
    posts = await res.json();
  });

  onDestroy(() => {
    console.log('Component removed');
  });
</script>

<ul>
  {#each posts as post (post.id)}
    <li>{post.title}</li>
  {/each}
</ul>
```

| Function | When it runs | Runs on the server? |
|---|---|---|
| `onMount` | After the component is first added to the DOM | No |
| `onDestroy` | Just before the component is removed | Yes |

Details:

- `onMount` may return a **cleanup function**, which runs when the component is destroyed. If you pass an `async` function, it returns a promise, so any cleanup you return from it will not run.
- Use `onMount` for browser-only setup, such as measuring elements or starting timers.
- The older `beforeUpdate` and `afterUpdate` hooks are not available in runes components. Use `$effect.pre` and `$effect` instead.

---

## 7.2 Effect cleanup

An `$effect` can return a function that cleans up after the previous run. It runs **before the effect re-runs** and **when the component is destroyed**.

```svelte
<script>
  let interval = $state(1000);
  let ticks = $state(0);

  $effect(() => {
    const id = setInterval(() => {
      ticks += 1;
    }, interval);

    // Cleanup: stop the old timer before starting a new one
    return () => clearInterval(id);
  });
</script>

<input type="range" min="100" max="2000" step="100" bind:value={interval} />
<p>Every {interval} ms, ticks: {ticks}</p>
```

When you move the slider, `interval` changes, so the effect cleans up the old timer and starts a new one. Without cleanup, timers would pile up.

Things that always need cleanup: timers, event listeners added by hand, subscriptions, observers, and open connections.

**Reading state without tracking it.** If you read a value inside an effect but do not want a change to re-run it, wrap the read in `untrack`:

```svelte
<script>
  import { untrack } from 'svelte';

  let query = $state('');
  let log = $state([]);

  $effect(() => {
    // Re-runs when query changes, but not when log changes
    const current = query;
    untrack(() => log.push(current));
  });
</script>
```

---

## 7.3 tick and timing

When you change state, Svelte does **not** update the DOM instantly. It batches changes and applies them together in the next microtask. If you need to read the DOM *after* the update, wait with `tick()`.

```svelte
<script>
  import { tick } from 'svelte';

  let messages = $state(['Hello']);
  let box;

  async function send() {
    messages.push('New message');

    // The new <li> is not in the DOM yet...
    await tick();

    // ...now it is, so scrolling to the bottom works
    box.scrollTop = box.scrollHeight;
  }
</script>

<ul bind:this={box} style="height: 100px; overflow: auto;">
  {#each messages as message, i (i)}
    <li>{message}</li>
  {/each}
</ul>

<button onclick={send}>Send</button>
```

`await tick()` returns a promise that resolves once pending state changes have been applied to the DOM. In many cases a `$effect` is a neater way to react to updates, because effects already run after the DOM is updated.

---

## 7.4 Working with the DOM

Sometimes you need the real DOM element, for example to focus an input, draw on a canvas, or hand it to a third-party library. Use `bind:this`.

```svelte
<script>
  let canvas = $state();

  $effect(() => {
    if (!canvas) return;
    const ctx = canvas.getContext('2d');
    ctx.fillStyle = 'tomato';
    ctx.fillRect(10, 10, 100, 60);
  });
</script>

<canvas bind:this={canvas} width="200" height="100"></canvas>
```

Remember:

- The variable is `undefined` until the element exists, so only use it inside `onMount`, `$effect`, or event handlers.
- Declare it with `$state()` if an effect needs to react when it becomes available.
- Never read `window`, `document`, or `localStorage` at the top level of a component in SvelteKit. The code also runs on the server, where they do not exist. Put such code in `onMount` or `$effect`, or check `browser` from `$app/environment`.
- Prefer Svelte's own tools (bindings, events, transitions) first. Reach for direct DOM access when they do not cover your need.

[Previous](./[6]-Shared-State-And-Stores.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[8]-Transitions-And-Animations.md)