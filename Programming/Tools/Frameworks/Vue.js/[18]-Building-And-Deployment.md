[Previous](./[17]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[19]-Next-Steps-With-Vue.md)

*Ecosystem And Delivery*

# Lesson 18 - Building And Deployment

## 18.1 Vite production builds

During development, Vite serves your files unbundled for instant updates. For **production**, you create an optimized build:

```bash
npm run build
```

Vite then bundles, minifies, and splits your code into the `dist/` folder:

```
dist/
├── index.html
└── assets/
    ├── index-4f3a2b1c.js     # your app code (hashed file names)
    ├── index-9d8e7f6a.css
    └── logo-1a2b3c4d.svg
```

The hash in each file name changes whenever the content changes. This lets browsers cache files for a long time and still pick up new versions.

Preview the production build locally before deploying:

```bash
npm run preview
```

The `package.json` scripts created by `create-vue`:

| Script | Purpose |
|---|---|
| `npm run dev` | Development server with hot reload |
| `npm run build` | Production build into `dist/` |
| `npm run preview` | Serve the `dist/` folder locally |

The `dist/` folder contains only **static files** (HTML, CSS, JavaScript). It can be hosted anywhere that serves static files. Don't commit `dist/` to Git. Make sure it is in `.gitignore`.

If your app is not served from the domain root (for example `https://username.github.io/my-app/`), set the `base` option:

```js
// vite.config.js
export default defineConfig({
  base: '/my-app/',
  plugins: [vue()]
})
```

---

## 18.2 Environment variables

**Environment variables** keep configuration (such as API URLs) out of your code, so it can differ between development and production.

Create files in the project root:

```
.env                  # loaded in all modes
.env.development      # only with npm run dev
.env.production       # only with npm run build
.env.local            # local overrides, ignored by Git
```

Example `.env`:

```
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My Vue App
```

Access them with `import.meta.env`:

```js
const apiUrl = import.meta.env.VITE_API_URL

console.log(import.meta.env.MODE)   // 'development' or 'production'
console.log(import.meta.env.DEV)    // true in dev
console.log(import.meta.env.PROD)   // true in production
```

```vue
<template>
  <h1>{{ title }}</h1>
</template>

<script setup>
const title = import.meta.env.VITE_APP_TITLE
</script>
```

Critical rules:

- Only variables prefixed with **`VITE_`** are exposed to your client code.
- **Everything exposed is public.** It is embedded in the JavaScript that every visitor downloads. **Never put secrets** (database passwords, private API keys) in `VITE_` variables.
- Keep `.env.local` and any file with real credentials out of Git by adding them to `.gitignore`.
- Environment values are read at **build time**, so you must rebuild after changing them.
- Secrets belong on a server (such as Nuxt server routes or a separate backend).

---

## 18.3 Hosting options

Because a Vue SPA builds to static files, hosting is easy and often free.

| Host | How it works |
|---|---|
| **Netlify** | Connect a Git repo, set build command `npm run build` and publish directory `dist` |
| **Vercel** | Import the repo; it detects Vite automatically |
| **GitHub Pages** | Free hosting from a repository; use the `base` option and a GitHub Actions workflow |
| **Cloudflare Pages** | Git-based deploys on a global network |
| **Traditional server** | Upload `dist/` to Nginx, Apache, or similar |

**SPA fallback.** With `createWebHistory()`, visiting `/about` directly asks the server for `/about`, which does not exist as a file. The server must serve `index.html` for unknown paths so Vue Router can handle them.

Netlify, `public/_redirects`:

```
/*    /index.html   200
```

Nginx:

```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

Vercel, `vercel.json`:

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

Alternatively, use `createWebHashHistory()` (URLs like `/#/about`), which needs no server setup.

Apps that need server-side rendering (like Nuxt in SSR mode) require a host that runs Node.js or edge functions. Static generation (`nuxt generate`) works on any static host.

Hosting platforms change their interfaces and features often, so check their current documentation for exact steps.

---

## 18.4 CI/CD basics

**Continuous Integration (CI)** automatically checks every change (lint, type check, tests, build). **Continuous Deployment (CD)** automatically publishes passing changes. The idea is to catch problems early and deploy without manual steps.

A **GitHub Actions** workflow lives in `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Type check
        run: npm run type-check

      - name: Run tests
        run: npm test -- --run

      - name: Build
        run: npm run build
```

Notes:

- `npm ci` installs exact versions from `package-lock.json`. It is faster and more reliable than `npm install` in automation.
- `npm test -- --run` makes Vitest run once instead of staying in watch mode.
- Remove the type-check step if your project does not use TypeScript.
- Pull requests that fail the workflow should not be merged.

Deploying automatically: hosts like Netlify, Vercel, and Cloudflare Pages deploy on every push to `main` once connected, so you often need only the CI workflow for checks. Store secrets (deploy tokens) in the repository's **Settings → Secrets**, never in the workflow file.

A healthy delivery routine:

1. Work on a branch.
2. Open a pull request. CI runs automatically.
3. Review and merge when CI passes.
4. The host builds and deploys `main`.
5. Check the live site, and keep the ability to roll back to a previous deployment.

---

[Previous](./[17]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[19]-Next-Steps-With-Vue.md)