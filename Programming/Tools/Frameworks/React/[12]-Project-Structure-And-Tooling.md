[Previous](./[11]-Styling-React-Apps.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[13]-Performance-Optimization.md)

*Building Applications*

# Lesson 12 - Project Structure And Tooling

## 12.1 Vite and build tooling

Browsers cannot run JSX, TypeScript, or imported CSS directly. A **build tool** transforms and bundles your source code. **Vite** does this in two modes:

- **Development:** serves your files as native ES modules on demand and applies instant updates (HMR). Startup is fast because it doesn't bundle everything first.
- **Production build:** bundles, minifies, and optimizes everything into static files in `dist/`.

### vite.config.js

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    proxy: {
      "/api": "http://localhost:8080",   // forward /api requests to a backend
    },
  },
  resolve: {
    alias: {
      "@": "/src",                      // import from "@/components/..."
    },
  },
});
```

- **`plugins`:** `@vitejs/plugin-react` adds JSX support and React Fast Refresh.
- **`server.proxy`:** avoids CORS problems during development by forwarding API calls to your backend.
- **`resolve.alias`:** lets you write `import Button from "@/components/Button"` instead of long `../../../` paths. For TypeScript, mirror the alias in `tsconfig.json` (`paths`) so the editor understands it.

### Other tools you may meet

| Tool | Purpose |
|---|---|
| **npm / pnpm / yarn** | Install and manage dependencies |
| **TypeScript** | Adds static types |
| **Babel / SWC / esbuild** | Transform modern syntax and JSX |
| **Webpack** | Older, highly configurable bundler (used by Create React App, now deprecated) |
| **Next.js / React Router framework mode** | Frameworks with their own build setups |

### package.json

`dependencies` are needed at runtime (React). `devDependencies` are build/development tools (Vite, ESLint). Install the latter with `npm install -D <package>`.

---

## 12.2 Folder organization patterns

React doesn't require any structure. Choose one that stays understandable as the project grows.

### Pattern 1: Group by type

```text
src/
├── components/
│   ├── Button.jsx
│   └── Navbar.jsx
├── hooks/
│   └── useFetch.js
├── pages/
│   ├── Home.jsx
│   └── Dashboard.jsx
├── context/
├── utils/
└── main.jsx
```

Easy to start with, but related files end up far apart as the project grows.

### Pattern 2: Group by feature

```text
src/
├── features/
│   ├── auth/
│   │   ├── components/LoginForm.jsx
│   │   ├── hooks/useAuth.js
│   │   ├── api.js
│   │   └── index.js            # the feature's public exports
│   └── cart/
│       ├── components/
│       ├── store.js
│       └── index.js
├── components/                  # shared, generic UI (Button, Modal)
├── hooks/                       # shared hooks
├── lib/                         # configured libraries (axios instance, queryClient)
├── pages/ (or routes/)
└── main.jsx
```

Everything about one feature lives together, which makes it easier to find, change, or delete.

### Guidelines

- **Start simple.** A flat `components/` folder is fine for small apps. Reorganize when it gets hard to navigate.
- **Colocate** things used by only one component (its CSS, tests, small helpers) beside it.
- **Promote** code to a shared location only when a second place needs it.
- **One component per file**, with the file named after the component (`UserCard.jsx`).
- **Use consistent naming:** `PascalCase` for components, `camelCase` for hooks and utilities (`useFetch.js`, `formatDate.js`).
- **Barrel files** (`index.js` re-exporting things) can give clean imports but can hurt build performance and tree shaking if overused.
- Avoid very deep nesting. More than three or four levels is usually a sign to simplify.

---

## 12.3 ESLint and Prettier

### ESLint: finds problems

**ESLint** analyzes code for likely bugs and style issues. Vite's React template includes an `eslint.config.js` (flat config format) with React-specific rules:

- **`eslint-plugin-react-hooks`** enforces the Rules of Hooks and checks effect dependencies (`exhaustive-deps`). It is especially valuable.
- **`eslint-plugin-react-refresh`** warns about patterns that break Fast Refresh.

Run it:

```bash
npm run lint
```

Most editors (such as VS Code with the ESLint extension) show warnings as you type. Fix warnings instead of disabling the rules, especially the hooks rules.

### Prettier: formats code

**Prettier** automatically formats code (indentation, quotes, line width), ending style debates in teams.

```bash
npm install -D prettier
```

Create `.prettierrc`:

```json
{
  "semi": true,
  "singleQuote": false,
  "printWidth": 80,
  "trailingComma": "es5"
}
```

Add scripts to `package.json`:

```json
{
  "scripts": {
    "format": "prettier --write src",
    "format:check": "prettier --check src"
  }
}
```

Enable "format on save" in your editor for the best experience.

### Using both

ESLint checks **correctness**; Prettier handles **formatting**. Keep their roles separate. If ESLint formatting rules conflict with Prettier, add `eslint-config-prettier` to turn the conflicting ESLint rules off.

### Git hooks (optional)

Tools like **Husky** and **lint-staged** can run ESLint and Prettier automatically on files before each commit, so problems never reach the repository.

---

## 12.4 Environment variables

Environment variables hold configuration that changes between environments (development, staging, production), such as an API URL.

### In Vite

Create files in the project root:

```text
.env                # loaded in all modes
.env.local          # local overrides, ignored by git
.env.development    # loaded by `npm run dev`
.env.production     # loaded by `npm run build`
```

Only variables prefixed with **`VITE_`** are exposed to your client code:

```text
# .env
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App
SECRET_KEY=abc123          # NOT exposed (no VITE_ prefix)
```

Read them with `import.meta.env`:

```jsx
const apiUrl = import.meta.env.VITE_API_URL;

function Footer() {
  return <p>{import.meta.env.VITE_APP_TITLE}</p>;
}
```

Built-in values:

```js
import.meta.env.MODE      // "development" or "production"
import.meta.env.DEV       // true during `npm run dev`
import.meta.env.PROD      // true in a production build
import.meta.env.BASE_URL  // the base path the app is served from
```

### Critical safety rule

**Everything in a client-side app is public.** Values with the `VITE_` prefix are written into the JavaScript bundle that every visitor downloads. Never put real secrets (private API keys, database passwords) in them. Keep secrets on a **server** and have your app call that server.

### Other notes

- Add `.env.local` and `.env*.local` to `.gitignore`.
- Variables are replaced at **build time**. Changing them requires rebuilding.
- Restart the dev server after editing `.env` files.
- With TypeScript, declare types for your variables in `src/vite-env.d.ts`.

[Previous](./[11]-Styling-React-Apps.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[13]-Performance-Optimization.md)