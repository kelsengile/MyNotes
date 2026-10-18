[Previous](./[16]-Accessibility-And-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[18]-Next-Steps-With-React.md)

*Going Further*

# Lesson 17 - Building And Deployment

## 17.1 Production builds

During development, Vite serves unoptimized code with helpful warnings. A **production build** prepares your app for real users.

```bash
npm run build
```

This creates a `dist/` folder:

```text
dist/
├── index.html
└── assets/
    ├── index-a1b2c3d4.js       # bundled, minified JavaScript
    ├── index-e5f6g7h8.css      # bundled, minified CSS
    └── logo-9i0j1k2l.svg
```

What the build does:

- **Bundles** many modules into a few files.
- **Minifies** code (removes whitespace, shortens names).
- **Tree-shakes** unused code.
- **Splits chunks** for lazy-loaded modules (see [Lesson 13](./[13]-Performance-Optimization.md)).
- **Adds content hashes** to file names (`index-a1b2c3d4.js`). When a file changes, its name changes, so browsers can cache files for a long time safely and still get updates.
- Turns React into its **production mode** (faster, without development warnings).

### Test the build locally

Never assume the build behaves like the dev server:

```bash
npm run preview
```

This serves `dist/` at a local address (by default `http://localhost:4173`). Click through the app, especially routes and anything using environment variables. `preview` is for testing only, not for real hosting.

### Checking size

The build output lists each file's size. If a chunk is very large, investigate with a bundle visualizer such as `rollup-plugin-visualizer` and consider code splitting or lighter dependencies.

---

## 17.2 Hosting static apps

A client-side React app builds to **static files** (HTML, JS, CSS). Any host that can serve static files will work. You do not need to run a Node.js server.

### Popular options

| Host | Notes |
|---|---|
| **Vercel** | Connect a Git repo; it detects Vite and deploys on every push |
| **Netlify** | Similar Git-based workflow, with drag-and-drop deploys too |
| **Cloudflare Pages** | Fast global network, Git-based |
| **GitHub Pages** | Free, good for demos and portfolios |
| **Firebase Hosting** | Simple CLI-based deployment |
| **Your own server** | Nginx, Apache, or object storage with a CDN |

For Git-based hosts, the typical settings are:

- **Build command:** `npm run build`
- **Output directory:** `dist`

### Client-side routing needs a fallback

If your app uses React Router, a user visiting `https://example.com/dashboard` directly (or refreshing) makes the server look for a file called `/dashboard`, which doesn't exist, so it returns a **404**. The server must instead return `index.html` for unknown paths, and let React Router handle the route.

**Netlify:** create `public/_redirects`:

```text
/*    /index.html   200
```

**Vercel:** create `vercel.json`:

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

**Nginx:**

```nginx
location / {
  try_files $uri /index.html;
}
```

### GitHub Pages and the base path

GitHub Pages serves a project at `https://username.github.io/repo-name/`, a sub-path. Tell Vite about it in `vite.config.js`:

```js
export default defineConfig({
  plugins: [react()],
  base: "/repo-name/",
});
```

Also set the router's `basename`:

```jsx
const router = createBrowserRouter(routes, {
  basename: "/repo-name",
});
```

GitHub Pages doesn't support rewrites, so for client-side routes a common workaround is a `404.html` redirect trick, or using a hash-based router (`createHashRouter`).

### Caching tips

- Hashed files in `assets/` can be cached for a long time.
- `index.html` should **not** be cached for long, since it points to the latest hashed files. Most static hosts configure this correctly by default.

---

## 17.3 Environment configuration

Your app often needs different settings in different environments (development, staging, production).

### Build-time variables

As covered in [Lesson 12](./[12]-Project-Structure-And-Tooling.md), Vite reads `VITE_`-prefixed variables at **build time**, and writes them into the bundle:

```text
# .env.production
VITE_API_URL=https://api.example.com
```

```js
const apiUrl = import.meta.env.VITE_API_URL;
```

### Setting variables on your host

Do not commit production `.env` files that contain environment-specific values. Instead, add variables in your hosting provider's dashboard (usually under *Settings > Environment variables*) so they are available when the host runs `npm run build`. If you change a variable, **trigger a new build**.

### Environments

| Environment | Purpose | Typical API |
|---|---|---|
| Development | Your machine | Local backend or mocks |
| Staging / Preview | Test changes before release | Test backend |
| Production | Real users | Real backend |

Most hosts create **preview deployments** for each pull request, which makes it easy to review changes in a live URL.

### Security reminder

Anything that ends up in a built static app is **visible to anyone**. Only public configuration (public API URLs, public keys designed for browsers) belongs in `VITE_` variables. Secrets must stay on a server.

### Building one build for many environments

Since variables are baked in at build time, one build cannot be reused across environments. If you need that, fetch a `config.json` at startup, or have the server inject configuration. For most projects, one build per environment is fine.

### Monitoring and analytics

Production apps benefit from:

- **Error tracking** (for example Sentry) to catch crashes.
- **Analytics** to understand usage.
- **Performance monitoring** (Core Web Vitals via Lighthouse or real-user monitoring).

---

## 17.4 CI/CD basics

- **Continuous Integration (CI):** automatically **build and test** every change.
- **Continuous Delivery/Deployment (CD):** automatically **release** passing changes.

Together they catch problems before users do and make releases routine.

### GitHub Actions example

Create `.github/workflows/ci.yml`:

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
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - run: npm ci
      - run: npm run lint
      - run: npx vitest run
      - run: npm run build
```

What each step does:

- **`checkout`** downloads your code.
- **`setup-node`** installs Node.js and caches dependencies.
- **`npm ci`** installs exact versions from `package-lock.json`, which is faster and more reproducible than `npm install` in CI.
- **lint, test, build** fail the workflow if anything is wrong. A red check on a pull request tells reviewers not to merge it.

Check the current action versions and recommended Node.js version when you set this up; they change over time.

### Deploying from CI

Many hosts (Vercel, Netlify, Cloudflare Pages) **deploy automatically** when you push to your repository, so you may not need to write deployment steps yourself. For GitHub Pages, you can use the official Pages actions to upload `dist/` and deploy.

### A typical workflow

1. Create a **branch** for your change.
2. Push it and open a **pull request**.
3. CI runs lint, tests, and build. The host creates a **preview deployment**.
4. A teammate **reviews** the code and the preview.
5. **Merge** into `main`.
6. CI passes and the host **deploys to production** automatically.

### Good practices

- Keep the `main` branch always deployable.
- Use **branch protection** so changes can only merge when checks pass.
- Keep pipelines **fast**; slow CI gets ignored.
- Store secrets in the CI provider's **secrets** feature, never in the repository.
- Make it easy to **roll back** to a previous deployment if something breaks. Most hosts keep previous deployments available to re-activate.

[Previous](./[16]-Accessibility-And-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[18]-Next-Steps-With-React.md)