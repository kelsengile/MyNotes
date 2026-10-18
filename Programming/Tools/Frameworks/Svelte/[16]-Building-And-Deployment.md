[Previous](./[15]-Performance-And-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[17]-Next-Steps-With-Svelte.md)

*Quality And Delivery*

# Lesson 16 - Building And Deployment

## 16.1 Production builds

During development, `npm run dev` serves unoptimized code with live reload. For the real site, create a **production build**:

```bash
npm run build
```

This compiles components, bundles and minifies code, splits it per route, prerenders pages that ask for it, and finally runs your **adapter** to produce the output your host needs.

Test it locally before deploying:

```bash
npm run preview
```

The preview server (usually at `http://localhost:4173`) serves the production build. It is for checking only and is not meant for real traffic.

Pre-deploy checklist:

- `npm run check` and your tests pass.
- Build finishes without errors or surprising warnings.
- Pages work in the preview, including forms and navigation.
- Environment variables are set for production (see 16.3).
- The correct adapter is selected for your host.

---

## 16.2 Hosting choices

| Option | Adapter | Good for |
|---|---|---|
| Static host (GitHub Pages, Netlify, Cloudflare Pages) | `adapter-static` | Blogs, docs, portfolios. Cheap and simple |
| Serverless platforms (Vercel, Netlify, Cloudflare) | Matching adapter | Dynamic sites with little server management |
| Node server (VPS, Docker, Railway, Fly.io) | `adapter-node` | Full control, long-running processes, WebSockets |

**GitHub Pages note:** sites are served from a sub-path (`username.github.io/repo`), so set a base path in `svelte.config.js` and add a `.nojekyll` file in `static/`:

```js
export default {
  kit: {
    paths: {
      base: process.env.NODE_ENV === 'production' ? '/my-repo' : ''
    }
  }
};
```

**Docker example** for `adapter-node`:

```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
RUN npm prune --omit=dev

FROM node:22-alpine
WORKDIR /app
COPY --from=build /app/build ./build
COPY --from=build /app/node_modules ./node_modules
COPY package.json .
ENV NODE_ENV=production
EXPOSE 3000
CMD ["node", "build"]
```

Build and run it with `docker build -t my-app .` and `docker run -p 3000:3000 my-app`.

---

## 16.3 Environment variables

Settings such as API keys and database URLs should come from the **environment**, never from source code. In development, put them in a `.env` file in the project root:

```
DATABASE_URL=postgres://user:pass@localhost/mydb
SECRET_KEY=super-secret
PUBLIC_API_URL=https://api.example.com
```

Import them through SvelteKit's special modules:

| Module | Visible to browser? | When values are read |
|---|---|---|
| `$env/static/private` | No | Build time |
| `$env/dynamic/private` | No | Runtime (when the server runs) |
| `$env/static/public` | Yes | Build time |
| `$env/dynamic/public` | Yes | Runtime |

```js
// Server-only code
import { SECRET_KEY } from '$env/static/private';
import { env } from '$env/dynamic/private';

console.log(env.DATABASE_URL);
```

```svelte
<script>
  import { PUBLIC_API_URL } from '$env/static/public';
</script>

<p>API: {PUBLIC_API_URL}</p>
```

Rules:

- Only variables starting with `PUBLIC_` can reach the browser. Everything else stays on the server.
- Private modules cannot be imported by client code, and SvelteKit errors if you try.
- **Never commit `.env` to Git.** Make sure it is in `.gitignore`, and set the variables in your host's dashboard for production.
- Use `static` when the value is fixed at build time (it can be optimized). Use `dynamic` when the same build must run with different settings.

---

## 16.4 CI/CD basics

**Continuous Integration (CI)** automatically checks every change. **Continuous Delivery/Deployment (CD)** automatically ships changes that pass. On GitHub, you do this with **GitHub Actions**. Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - run: npm ci
      - run: npm run lint
      - run: npm run check
      - run: npm run build
```

Add a test step (such as `npm run test:unit -- --run`) once you have tests. The exact script names depend on the options chosen when the project was created, so check your `package.json`.

How the pipeline works:

1. You push a branch or open a pull request.
2. The workflow installs dependencies with `npm ci` (a clean, reproducible install).
3. It runs lint, type checks, tests, and a build. If any step fails, the change is flagged.
4. When changes reach `main`, a hosting service connected to your repository (Vercel, Netlify, Cloudflare Pages) builds and deploys automatically. Or add a deploy step to the workflow.

Store secrets such as deploy tokens in your repository's **Settings → Secrets**, never in the workflow file.

[Previous](./[15]-Performance-And-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[17]-Next-Steps-With-Svelte.md)