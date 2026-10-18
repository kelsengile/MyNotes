[Previous](./[16]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md)

*Quality And Delivery*

# Lesson 17 - Next Steps With Svelte

## 17.1 UI libraries

You do not have to design every button yourself. Popular options fall into three groups.

| Type | Examples | What you get |
|---|---|---|
| **Component kits** | shadcn-svelte, Skeleton, Flowbite Svelte | Ready-made styled components |
| **Headless libraries** | Bits UI, Melt UI | Accessible behavior (dialogs, menus, tabs) with no styling, so you style them yourself |
| **CSS frameworks** | Tailwind CSS, DaisyUI | Utility classes or class-based themes |

Add Tailwind CSS to a project with one command:

```bash
npx sv add tailwindcss
```

How to choose:

- Prioritize **accessibility**. Check that dialogs trap focus and menus work with the keyboard.
- Confirm the library supports **Svelte 5** and runes.
- Prefer libraries that let you own and change the code, or that are easy to theme.
- Do not add a library for something a few lines of CSS can do.

---

## 17.2 Svelte ecosystem tools

Tools you will meet as your projects grow:

| Need | Tools to explore |
|---|---|
| Forms and validation | Superforms with Zod or Valibot |
| Database access | Drizzle ORM, Prisma |
| Authentication | Better Auth, Auth.js |
| Markdown content | mdsvex (use Markdown with Svelte components) |
| Translations (i18n) | Paraglide |
| Toast messages | svelte-sonner |
| Data fetching and caching | TanStack Query for Svelte |
| Charts | LayerChart and other chart libraries |

The `sv` command can set up many of these for you:

```bash
npx sv add
```

It shows a menu of add-ons (such as Drizzle, Tailwind, mdsvex, Paraglide, and testing tools) and edits your project's configuration files.

Where to keep learning:

- The official tutorial at **svelte.dev/tutorial** (interactive, in the browser)
- The Svelte and SvelteKit documentation at **svelte.dev/docs**
- The **Svelte Playground** to experiment with small ideas
- The Svelte Discord community and the Svelte Society

Tool popularity changes quickly. Before adopting a library, check that it is actively maintained.

---

## 17.3 Migrating from Svelte 4

Svelte 5 still runs most Svelte 4 code, so you can migrate gradually, component by component. A component either uses runes or the old syntax, never both.

Run the automatic migration tool:

```bash
npx sv migrate svelte-5
```

It rewrites most components and adds comments where it needs your help. For SvelteKit 1 projects, there is also `npx sv migrate sveltekit-2`.

Before and after:

```svelte
<!-- Svelte 4 -->
<script>
  import { createEventDispatcher } from 'svelte';

  export let count = 0;
  $: doubled = count * 2;

  const dispatch = createEventDispatcher();
</script>

<button on:click={() => dispatch('add')}>{count} / {doubled}</button>
<slot />
```

```svelte
<!-- Svelte 5 -->
<script>
  let { count = 0, onadd, children } = $props();
  let doubled = $derived(count * 2);
</script>

<button onclick={() => onadd?.()}>{count} / {doubled}</button>
{@render children?.()}
```

| Change | Svelte 4 | Svelte 5 |
|---|---|---|
| Props | `export let` | `$props()` |
| Reactive values | `$:` | `$derived` / `$effect` |
| Events | `on:click` | `onclick` |
| Component events | `createEventDispatcher` | Callback props |
| Slots | `<slot>` | Snippets and `{@render}` |
| Event modifiers | `on:click\|preventDefault` | Call `event.preventDefault()` |

Tips:

- Commit your work first, so you can review what the tool changed.
- Check any `$effect` the tool creates. Often a `$derived` is the cleaner choice.
- Update third-party libraries to versions that support Svelte 5.
- Migrate leaf components first and work your way up.

---

## 17.4 Project ideas

The best way to learn is to build. Here are projects that grow in difficulty, and the lessons each one practices.

| Project | Skills practiced |
|---|---|
| **Todo list** with filters and a counter | `$state`, `$derived`, `{#each}`, events (Lessons 2, 3, 5) |
| **Quiz app** with score and timer | Props, callbacks, effects with cleanup (Lessons 4, 7) |
| **Kanban board** with animated cards | Keyed each blocks, `animate:flip`, transitions (Lesson 8) |
| **Weather dashboard** using a public API | `load` functions, error handling, streaming (Lessons 10, 11) |
| **Personal blog** with Markdown posts | Routing, dynamic params, prerendering, mdsvex (Lessons 9, 12) |
| **Notes app** with login and a database | Form actions, hooks, `locals`, API endpoints (Lessons 11, 12) |
| **Shopping cart** | Shared runes state, derived totals, context (Lessons 4, 6) |

How to approach a project:

1. Write down three to five features and build only the smallest version first.
2. Add **TypeScript**, linting, and a few tests early (Lessons 13 and 14).
3. Check accessibility and performance before calling it done (Lesson 15).
4. **Deploy** it and share the link (Lesson 16). A live project teaches more than a local one.
5. Read other people's open source Svelte projects to see how experienced developers organize code.

Congratulations on completing the Svelte Topic. Keep experimenting, and welcome to the community.

[Previous](./[16]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md)