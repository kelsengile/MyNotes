[Previous](./[7]-Lifecycle-And-Effects.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[9]-SvelteKit-Routing.md)

*Components In Depth*

# Lesson 8 - Transitions And Animations

Svelte includes motion tools so you do not need extra libraries. A **transition** plays when an element enters or leaves the DOM. An **animation** plays when an element moves position inside a list.

## 8.1 Built-in transitions

Import transitions from `svelte/transition` and apply them with `transition:`.

```svelte
<script>
  import { fade, fly, slide } from 'svelte/transition';

  let visible = $state(true);
</script>

<button onclick={() => (visible = !visible)}>Toggle</button>

{#if visible}
  <p transition:fade>I fade in and out</p>
  <p transition:fly={{ y: 20, duration: 400 }}>I fly in from below</p>
  <p transition:slide>I slide open and shut</p>
{/if}
```

Available built-ins: `fade`, `blur`, `fly`, `slide`, `scale`, `draw` (for SVG paths), and `crossfade`.

Options:

- Pass parameters as an object: `{{ duration: 300, delay: 100 }}`.
- Use `in:` and `out:` instead of `transition:` to give an element **different** enter and exit effects: `<p in:fly={{ y: 20 }} out:fade>`.
- Transitions are **local** by default: they play only when their own block is added or removed, not when a parent block is. Add `|global` (`transition:fade|global`) to make them also play for parent changes.
- Elements fire events such as `onintrostart`, `onintroend`, `onoutrostart`, and `onoutroend`.

---

## 8.2 Animations

The `animate:` directive animates an element when its **position changes** because other items were added, removed, or reordered. It only works on the direct child of a **keyed** `{#each}` block.

```svelte
<script>
  import { flip } from 'svelte/animate';
  import { fade } from 'svelte/transition';

  let items = $state([
    { id: 1, name: 'One' },
    { id: 2, name: 'Two' },
    { id: 3, name: 'Three' }
  ]);

  function shuffle() {
    items = [...items].sort(() => Math.random() - 0.5);
  }

  function remove(id) {
    items = items.filter((item) => item.id !== id);
  }
</script>

<button onclick={shuffle}>Shuffle</button>

<ul>
  {#each items as item (item.id)}
    <li animate:flip={{ duration: 300 }} transition:fade>
      {item.name}
      <button onclick={() => remove(item.id)}>x</button>
    </li>
  {/each}
</ul>
```

`flip` stands for **First, Last, Invert, Play**. It measures where an element was and where it ends up, then smoothly moves it between the two. The `crossfade` function from `svelte/transition` can send an item from one list to another while it animates.

---

## 8.3 Custom transitions

A transition is just a function. It receives the DOM node and parameters and returns an object describing the effect.

```svelte
<script>
  import { cubicOut } from 'svelte/easing';

  function spin(node, { duration = 600 } = {}) {
    return {
      duration,
      easing: cubicOut,
      css: (t, u) => `
        transform: rotate(${u * 360}deg) scale(${t});
        opacity: ${t};
      `
    };
  }

  let visible = $state(true);
</script>

<button onclick={() => (visible = !visible)}>Toggle</button>

{#if visible}
  <h2 transition:spin={{ duration: 800 }}>Spinning in and out</h2>
{/if}
```

The returned object can have:

| Key | Meaning |
|---|---|
| `delay` | Milliseconds to wait before starting |
| `duration` | Milliseconds the transition lasts |
| `easing` | A function that shapes the speed curve (from `svelte/easing`) |
| `css(t, u)` | Returns a CSS string. `t` goes from 0 to 1 when entering, and `u` is `1 - t` |
| `tick(t, u)` | Runs JavaScript on every frame (use only when CSS cannot do the job) |

Prefer `css` over `tick`. CSS transitions run off the main thread, so they stay smooth.

---

## 8.4 Motion with tweened and spring

Sometimes you want a **number** to move smoothly instead of jumping, for example a progress bar or a counter. The `svelte/motion` module has two classes for this.

**`Tween`** moves to the new value over a fixed time:

```svelte
<script>
  import { Tween } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';

  const progress = new Tween(0, { duration: 600, easing: cubicOut });
</script>

<progress value={progress.current}></progress>

<button onclick={() => (progress.target = 0)}>0%</button>
<button onclick={() => (progress.target = 0.5)}>50%</button>
<button onclick={() => (progress.target = 1)}>100%</button>
```

**`Spring`** behaves like a physical spring. It overshoots and settles, and works well for interactive motion such as following the mouse:

```svelte
<script>
  import { Spring } from 'svelte/motion';

  const coords = new Spring({ x: 50, y: 50 }, { stiffness: 0.1, damping: 0.25 });
</script>

<svelte:window onmousemove={(e) => (coords.target = { x: e.clientX, y: e.clientY })} />

<div
  style="position: fixed; left: {coords.current.x}px; top: {coords.current.y}px;
         width: 20px; height: 20px; border-radius: 50%; background: tomato;"
></div>
```

- Set `.target` to give the new goal, and read `.current` for the value to display.
- Lower `stiffness` is looser, and lower `damping` is bouncier.
- Older projects use the `tweened()` and `spring()` stores from the same module. They still work, but the classes above are recommended for new runes code.

[Previous](./[7]-Lifecycle-And-Effects.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[9]-SvelteKit-Routing.md)