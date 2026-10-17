[Previous](./[17]-UI-With-Angular-Material.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[19]-Next-Steps-With-Angular.md)

*Quality And Delivery*

# Lesson 18 - Building And Deployment

Building turns your TypeScript and templates into optimized files browsers can run. Deployment puts those files somewhere users can reach. This lesson walks the whole path.

---

## 18.1 ng build and environments

```bash
ng build
```

By default this runs the **production** configuration: ahead-of-time (AOT) compilation, minification, tree-shaking, and hashed file names for caching. The result goes to `dist/<project-name>/` (the files to host are usually in its `browser` subfolder). You will see a size table and any lazy chunks (Lesson 10.4).

Configurations are defined in `angular.json`:

```bash
ng build                              # production (default)
ng build --configuration development # unminified, with source maps
```

Common checks:

- **Budgets** in `angular.json` warn or fail when bundles exceed a size.
- `ng build --stats-json` plus a bundle analyzer shows what makes bundles large.

**Environment-specific values** (API addresses, feature flags) differ between local development and production. Recent projects don't create environment files automatically; generate them when needed:

```bash
ng generate environments
```

This creates `src/environments/environment.ts` (production defaults) and `environment.development.ts`:

```ts
// environment.ts
export const environment = {
  production: true,
  apiUrl: 'https://api.example.com',
};
```

```ts
// environment.development.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000',
};
```

Import `environment` from `src/environments/environment`. The development build configuration swaps in `environment.development.ts` through `fileReplacements` in `angular.json`.

> **Security:** everything in the frontend is public. Never put secrets (private API keys, passwords) in environment files. Anyone can read them from the downloaded JavaScript.

For one build serving many environments, load a `config.json` at startup instead of baking values in.

---

## 18.2 Server-side rendering

Lesson 16.4 explained *what* SSR and hydration are. Here is how it affects your **build and output**.

Create SSR support: `ng add @angular/ssr`. This adds a server entry point (`server.ts`), server config, and updates `angular.json`.

`ng build` then produces two outputs:

```
dist/my-app/
├── browser/     # static files for the client
└── server/      # Node server that renders pages
```

Run it with Node:

```bash
node dist/my-app/server/server.mjs
```

**Render mode per route** lets you choose how each page is produced, in `app.routes.server.ts`:

```ts
import { RenderMode, ServerRoute } from '@angular/ssr';

export const serverRoutes: ServerRoute[] = [
  { path: '',        renderMode: RenderMode.Prerender },  // built once at build time (SSG)
  { path: 'account', renderMode: RenderMode.Client },     // browser only
  { path: '**',      renderMode: RenderMode.Server },     // rendered per request
];
```

| Mode | When HTML is generated | Good for |
|---|---|---|
| `Prerender` | At build time | Marketing pages, docs, blogs (can be served from a CDN) |
| `Server` | On every request | Personalized or frequently changing pages |
| `Client` | In the browser | Private dashboards behind login |

Using the setting `"outputMode": "static"` in the build options creates a fully static site (no Node server needed), as long as every route is prerendered.

Things to watch:

- **Browser-only APIs** (`window`, `localStorage`) must be guarded (Lesson 16.4).
- **Duplicate HTTP requests:** Angular transfers server HTTP responses to the browser with `provideClientHydration`, avoiding refetching the same data.
- **Cookies and request context** on the server require care; do not store per-user data in module-level variables, since one server instance handles many users.

---

## 18.3 Hosting options

Hosting means serving your build output over HTTPS.

**Static hosting** (client-side apps or prerendered sites) is the simplest and cheapest:

| Host | Notes |
|---|---|
| Firebase Hosting | Official Angular integration (`ng add @angular/fire`) |
| Netlify, Vercel, Cloudflare Pages | Connect a Git repo; set the build command and publish folder |
| GitHub Pages | Free for public repos; needs a base href |
| AWS S3 + CloudFront, Azure Static Web Apps | Common in enterprises |

Settings you will meet:

- **Build command:** `ng build`
- **Publish directory:** `dist/<project-name>/browser`
- **Base href:** if the app lives in a sub-path (e.g. GitHub Pages `/my-repo/`), build with `ng build --base-href /my-repo/`.

**SPA fallback (important):** an Angular app with the router has URLs like `/tasks/42` that exist only in the browser. If a user refreshes, the host looks for a file named `tasks/42` and returns a 404. The server must send `index.html` for unknown paths. Example for Nginx:

```nginx
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

Static hosts like Netlify or Firebase have an equivalent rewrite setting (such as a `_redirects` file or `rewrites` rule).

**SSR apps** need a Node runtime: a container platform (Cloud Run, Azure App Service, AWS), or serverless adapters on platforms like Vercel and Netlify.

**Docker example:**

```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist/my-app/browser /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
```

Adjust the Node version and `my-app` to match your project.

Finally: serve over **HTTPS**, enable compression (gzip/brotli), and cache hashed files for a long time but never cache `index.html`.

---

## 18.4 CI/CD basics

**Continuous Integration (CI)** automatically builds and tests every change. **Continuous Delivery/Deployment (CD)** automatically ships passing builds.

A minimal pipeline does the same steps you would do by hand:

1. Install dependencies exactly as locked: `npm ci`
2. Lint: `ng lint` (if configured)
3. Test once, no watch: `ng test --no-watch`
4. Build: `ng build`
5. Deploy the output (on the main branch only)

Example with **GitHub Actions** (`.github/workflows/ci.yml`):

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
      - run: npm run lint --if-present
      - run: npm test -- --no-watch
      - run: npm run build

      - uses: actions/upload-artifact@v4
        with:
          name: dist
          path: dist/
```

Notes:

- If your tests run in Karma, add `--browsers=ChromeHeadless` since CI machines have no screen.
- `npm ci` is faster and safer than `npm install` in CI; it requires a committed lockfile.
- **Cache** `node_modules` (the `cache: npm` line) to speed up runs.
- Store credentials such as deploy tokens in the CI platform's **secrets**, never in the repository.
- Protect the main branch: require the pipeline to pass before merging.
- A good deploy has a rollback plan: keep previous builds so you can switch back quickly.

For larger repositories, tools like Nx (Lesson 19.1) can run only the tests and builds affected by a change.

---

**Further reading:** [Angular CLI build](https://angular.dev/tools/cli/build) · [SSR guide](https://angular.dev/guide/ssr)

[Previous](./[17]-UI-With-Angular-Material.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[19]-Next-Steps-With-Angular.md)
