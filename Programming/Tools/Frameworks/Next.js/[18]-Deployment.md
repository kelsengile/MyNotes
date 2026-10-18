[Previous](./[17]-Performance-Tuning.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[19]-Next-Steps-With-Nextjs.md)

*Quality And Delivery*

# Lesson 18 - Deployment

## 18.1 Deploying on Vercel

**Vercel**, the company that created Next.js, offers the most direct deployment path. It supports every Next.js feature without extra configuration: static pages, dynamic rendering, ISR, middleware, image optimization, and server actions.

Steps:

1. Push your project to a Git provider (GitHub, GitLab, or Bitbucket).
2. Create a Vercel account and choose **Add New Project**.
3. Import your repository. Vercel detects Next.js automatically.
4. Add environment variables in the project settings.
5. Click **Deploy**.

After that, every push triggers a build:

| Event | Result |
|---|---|
| Push to the main branch | Production deployment |
| Push to another branch or open a pull request | Preview deployment with its own URL |

You can also deploy from the command line:

```bash
npm install -g vercel
vercel          # preview deployment
vercel --prod   # production deployment
```

Features include custom domains with automatic HTTPS, a global CDN, rollbacks to previous deployments, and built-in analytics. Pricing and limits change over time, so check Vercel's current plans.

---

## 18.2 Self-hosting with Node and Docker

Next.js can run anywhere Node.js runs (a VPS, your own server, a container platform).

**Basic Node.js hosting:**

```bash
npm run build   # create the production build
npm run start   # start the server on port 3000
```

Run it behind a reverse proxy such as Nginx or Caddy for HTTPS, and keep it alive with a process manager such as PM2 or systemd.

**Docker** is common for portability. First enable the **standalone output**, which produces a minimal self-contained server:

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "standalone",
};

export default nextConfig;
```

A multi-stage Dockerfile:

```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
EXPOSE 3000
CMD ["node", "server.js"]
```

```bash
docker build -t my-next-app .
docker run -p 3000:3000 my-next-app
```

Self-hosting notes:

- With multiple server instances, the default in-memory/file caches are not shared; you may need a custom cache handler.
- You are responsible for image optimization resources, scaling, and monitoring.
- Match the Node.js version to what your Next.js version supports.

---

## 18.3 Static exports

A **static export** builds your app into plain HTML, CSS, and JavaScript files that can be hosted on any static host (GitHub Pages, Netlify, S3, Cloudflare Pages) with no Node.js server.

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "export",
};

export default nextConfig;
```

```bash
npm run build
```

The result is written to the `out/` folder. Upload that folder to your host.

Limitations, because there is no server:

| Not supported | Alternative |
|---|---|
| Dynamic rendering (`cookies()`, `headers()`) | Fetch in the browser |
| Route Handlers that read the request | External API |
| Server Actions | External API |
| Middleware | Host-level redirects |
| ISR / on-demand revalidation | Rebuild the site |
| Default image optimization | `images: { unoptimized: true }` or a custom loader |

Dynamic routes need `generateStaticParams` so every page is generated at build time.

When hosting under a sub-path (such as GitHub Pages at `/repo-name`), set `basePath`:

```ts
const nextConfig = { output: "export", basePath: "/repo-name" };
```

Best for: documentation, portfolios, marketing sites, and blogs that do not need server features.

---

## 18.4 Environment variables and secrets

Environment variables keep configuration and secrets out of your source code.

Next.js loads these files automatically:

| File | Used for |
|---|---|
| `.env` | Defaults for all environments |
| `.env.local` | Local secrets (never commit) |
| `.env.development` | Only `next dev` |
| `.env.production` | Only `next build` / `next start` |

```bash
# .env.local
DATABASE_URL="postgresql://user:pass@host:5432/db"
AUTH_SECRET="a-long-random-string"
NEXT_PUBLIC_SITE_URL="https://example.com"
```

The critical rule:

- Variables **without** a prefix are available only on the **server**.
- Variables starting with **`NEXT_PUBLIC_`** are inlined into the browser bundle at **build time**, so they are visible to everyone. Never put secrets there.

```ts
// Server only
const dbUrl = process.env.DATABASE_URL;

// Safe for client code
const siteUrl = process.env.NEXT_PUBLIC_SITE_URL;
```

Because `NEXT_PUBLIC_` values are baked in during the build, changing one requires a **rebuild**.

Good practices:

- Add `.env*.local` to `.gitignore`.
- Set production variables in your host's dashboard or secret manager, not in committed files.
- Use different values for development, preview, and production.
- Rotate any secret that has ever been committed or leaked.
- Validate variables at startup (see Lesson 15).

[Previous](./[17]-Performance-Tuning.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[19]-Next-Steps-With-Nextjs.md)