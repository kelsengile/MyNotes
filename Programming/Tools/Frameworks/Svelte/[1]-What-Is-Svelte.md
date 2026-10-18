[Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[2]-Components-And-Markup.md)

*Basics*

# Lesson 1 - What Is Svelte

## 1.1 The compiler approach

Most frameworks (React, Vue) ship a **runtime library** to the browser. That runtime reads your components while the app is running and figures out how to update the page.

Svelte works differently. It is a **compiler**: during your build step it reads your `.svelte` files and turns them into small, plain JavaScript functions that create and update DOM elements directly. When the app reaches the browser, there is very little framework code left, because most of the work was done at build time.

What this gives you:

- **Smaller bundles**: only the code your components actually need is shipped.
- **Fast startup**: less JavaScript to download, parse, and run.
- **Less to learn**: components look like HTML with a few extra features.

The trade-off is that you need a build step (a tool such as Vite does this for you) and the ecosystem is smaller than React's.

---

## 1.2 Svelte vs virtual DOM frameworks

A **virtual DOM** (VDOM) is an in-memory copy of the page. When state changes, a VDOM framework:

1. Re-runs your component to build a new virtual tree.
2. Compares ("diffs") the new tree with the old one.
3. Applies only the differences to the real DOM.

Svelte skips steps 1 and 2. Because the compiler already knows which parts of the template depend on which state, it generates code that updates exactly those parts when the state changes.

| Aspect | Virtual DOM frameworks | Svelte |
|---|---|---|
| When work happens | Mostly at runtime | Mostly at build time |
| Update strategy | Diff two trees | Run targeted update code |
| Runtime size | Larger (framework shipped) | Very small |
| Extra build step | Optional | Required |
| Syntax | JSX or templates + hooks | HTML-like templates + runes |

Neither approach is "always better". Svelte's approach tends to be lighter and simpler, while larger ecosystems may offer more ready-made libraries.

---

## 1.3 Creating a project

You need **Node.js** installed (a current LTS version is recommended). Svelte's official command line tool is called `sv`.

```bash
npx sv create my-app
cd my-app
npm install
npm run dev
```

The `sv create` command asks you questions, such as which template to use (choose **SvelteKit minimal** for a full app) and whether to use TypeScript. After `npm run dev`, open the address printed in the terminal (usually `http://localhost:5173`).

Common scripts in `package.json`:

| Command | What it does |
|---|---|
| `npm run dev` | Starts the development server with live reload |
| `npm run build` | Creates an optimized production build |
| `npm run preview` | Serves the production build locally to test it |

A typical SvelteKit project looks like this:

```
my-app/
├── src/
│   ├── lib/              # Reusable code, imported with $lib
│   ├── routes/           # Pages (+page.svelte lives here)
│   └── app.html          # The HTML shell
├── static/               # Files served as-is (favicon, robots.txt)
├── svelte.config.js      # Svelte and SvelteKit settings
├── vite.config.js        # Vite build settings
└── package.json
```

If you only want Svelte without SvelteKit (no routing or server features), run `npm create vite@latest` and pick the **Svelte** template.

---

## 1.4 Anatomy of a .svelte file

A component is a single file ending in `.svelte`. It can contain up to three parts, all optional, in any order:

```svelte
<script>
  // 1. Logic: variables, functions, imports
  let name = 'world';
</script>

<!-- 2. Markup: regular HTML plus Svelte features -->
<h1>Hello {name}!</h1>

<style>
  /* 3. Styles: scoped to this component */
  h1 {
    color: rebeccapurple;
  }
</style>
```

Key points:

- The `<script>` block runs **once for each instance** of the component.
- Anything in curly braces `{ }` inside the markup is a JavaScript expression.
- Markup does not need a single wrapper element, so you can have several top-level elements.
- A second kind of script, `<script module>`, runs once when the file is first loaded and is shared by all instances. It is used for things like exported helper values.

```svelte
<script module>
  // Runs once, shared by every instance of this component
  let instances = 0;
</script>

<script>
  // Runs for each instance
  instances += 1;
</script>

<p>This is the component instance number {instances}.</p>
```

[Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[2]-Components-And-Markup.md)