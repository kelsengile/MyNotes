[Previous](./[12]-Rendering-Modes-And-Adapters.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[14]-Testing-Svelte-Applications.md)

*Quality And Delivery*

# Lesson 13 - TypeScript And Tooling

## 13.1 TypeScript in components

**TypeScript** adds types to JavaScript so many mistakes are caught before the code runs. To use it, choose TypeScript when you run `npx sv create`, then add `lang="ts"` to your script tag.

```svelte
<script lang="ts">
  let count = $state(0);                       // inferred as number
  let user = $state<{ name: string } | null>(null);
  let doubled = $derived(count * 2);

  function greet(name: string): string {
    return `Hello, ${name}`;
  }
</script>
```

Notes:

- Svelte 5 understands type annotations natively, so simple type-only TypeScript needs no extra setup.
- Features that generate runtime code (such as `enum`) need a preprocessor, which SvelteKit projects already include via `vitePreprocess()` in `svelte.config.js`.
- Type checking is **not** done while building. Run it yourself with `svelte-check`, which a SvelteKit project provides as a script:

```bash
npm run check
```

- Plain `.ts` files work normally. Files that use runes must be named `.svelte.ts`.

---

## 13.2 Typing props and load functions

**Typing props.** Describe the props in an interface and annotate the destructured result:

```svelte
<script lang="ts">
  import type { Snippet } from 'svelte';

  interface Props {
    title: string;
    count?: number;
    onselect?: (id: number) => void;
    children?: Snippet;
  }

  let { title, count = 0, onselect, children }: Props = $props();
</script>

<h2>{title} ({count})</h2>
<button onclick={() => onselect?.(1)}>Select</button>
{@render children?.()}
```

- Use `Snippet` for children and snippet props. For a snippet with parameters, write `Snippet<[Item]>`.
- Props may be optional with `?`, or required.

**Typing load functions.** SvelteKit generates types for every route. Import them from the special `./$types` module:

```ts
// src/routes/blog/[slug]/+page.server.ts
import type { PageServerLoad } from './$types';
import { error } from '@sveltejs/kit';

export const load: PageServerLoad = async ({ params }) => {
  const post = await getPost(params.slug);
  if (!post) error(404, 'Not found');
  return { post };
};
```

```svelte
<!-- src/routes/blog/[slug]/+page.svelte -->
<script lang="ts">
  import type { PageProps } from './$types';

  let { data }: PageProps = $props();
</script>

<h1>{data.post.title}</h1>
```

`PageProps` types both `data` and `form`. Older projects write `let { data }: { data: PageData } = $props();` with `PageData` from `./$types`. The `$types` files are created when you run `npm run dev` or `npx svelte-kit sync`.

**Typing app-wide values.** Edit `src/app.d.ts` to describe `event.locals`, `App.Error`, and page state:

```ts
declare global {
  namespace App {
    interface Locals {
      user: { id: number; name: string } | null;
    }
  }
}

export {};
```

---

## 13.3 Linting and formatting

**Linting** finds likely bugs and style problems. **Formatting** makes code look consistent automatically. The standard tools are **ESLint** and **Prettier**, both with Svelte plugins. The easiest way to add them to an existing project:

```bash
npx sv add eslint prettier
```

This installs `eslint-plugin-svelte` and `prettier-plugin-svelte` and creates the config files. A Prettier config looks like this:

```json
{
  "useTabs": false,
  "singleQuote": true,
  "printWidth": 100,
  "plugins": ["prettier-plugin-svelte"],
  "overrides": [{ "files": "*.svelte", "options": { "parser": "svelte" } }]
}
```

Typical scripts in `package.json`:

```json
{
  "scripts": {
    "lint": "prettier --check . && eslint .",
    "format": "prettier --write ."
  }
}
```

Run `npm run lint` to check and `npm run format` to fix formatting. Running them in your CI pipeline (Lesson 16) keeps the whole team consistent.

---

## 13.4 Editor support

A good editor setup makes Svelte much easier to write.

| Editor | Setup |
|---|---|
| **VS Code** | Install the official **Svelte for VS Code** extension |
| **WebStorm / JetBrains IDEs** | Built-in Svelte support (enable it in the settings) |
| **Neovim, Sublime, others** | Use the Svelte language server (`svelte-language-server`) |

What you get:

- Syntax highlighting for `.svelte` files
- Autocomplete for props, components, and imports
- Error and warning messages from the compiler and TypeScript as you type
- Go to definition, rename symbol, and quick fixes

Helpful habits:

- Format on save, using Prettier as the default formatter.
- Run `npm run check` before committing.
- Restart the editor's TypeScript or Svelte server if types look stale after you add routes (running `npx svelte-kit sync` regenerates them).
- Try the **Svelte Playground** at svelte.dev/playground to test small ideas without any setup.

[Previous](./[12]-Rendering-Modes-And-Adapters.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[14]-Testing-Svelte-Applications.md)