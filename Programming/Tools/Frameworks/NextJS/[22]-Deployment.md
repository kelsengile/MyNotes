[Previous](./[21]-Testing-NestJS-Applications.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[23]-Next-Steps-With-NestJS.md)

*Quality And Delivery*

# Lesson 22 - Deployment

## 22.1 Building for production

In development you run TypeScript through the Nest CLI. In production you **compile to JavaScript** and run it with plain Node.js.

```bash
npm run build          # compiles src/ into dist/
npm run start:prod     # runs: node dist/main
```

Production checklist:

- Set `NODE_ENV=production`. Many libraries enable optimizations when it is set.
- Install only runtime dependencies on the server: `npm ci --omit=dev` (after building).
- Use `npm ci` (not `npm install`) for repeatable installs from `package-lock.json`.
- Disable `synchronize` in TypeORM and run **migrations** (Lesson 12).
- Read all settings from environment variables (Lesson 11).
- Listen on the port the platform gives you: `app.listen(process.env.PORT ?? 3000, '0.0.0.0')`. Binding to `0.0.0.0` is needed inside containers.
- Enable graceful shutdown so in-flight requests finish and connections close:

```ts
app.enableShutdownHooks(); // calls OnModuleDestroy, OnApplicationShutdown on SIGTERM
```

You can keep the process alive after crashes with a process manager (PM2, systemd) or, better, with your container platform's restart policy.

---

## 22.2 Docker

**Docker** packages your app and its environment into an **image** that runs identically everywhere. A **multi-stage build** keeps the final image small: one stage builds the code, the final stage contains only what is needed to run it.

```dockerfile
# Dockerfile

# ---- Stage 1: build ----
FROM node:22-alpine AS build
WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# ---- Stage 2: run ----
FROM node:22-alpine AS production
WORKDIR /app
ENV NODE_ENV=production

COPY package*.json ./
RUN npm ci --omit=dev && npm cache clean --force

COPY --from=build /app/dist ./dist

USER node
EXPOSE 3000
CMD ["node", "dist/main"]
```

Use the Node.js version that your project targets. Add a `.dockerignore` so you do not copy junk into the image:

```
node_modules
dist
.git
.env
.env.*
npm-debug.log
```

Build and run:

```bash
docker build -t my-nest-app .
docker run -p 3000:3000 --env-file .env.production my-nest-app
```

A `docker-compose.yml` for local development with PostgreSQL:

```yaml
services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://postgres:postgres@db:5432/app
      NODE_ENV: production
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: app
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

Run it with `docker compose up --build`. Note that `depends_on` only waits for the container to start, not for the database to be ready, so add a health check or retry logic for connections.

---

## 22.3 Environment setup

Typical environments:

| Environment | Purpose | Notes |
|---|---|---|
| **Development** | Local coding | `synchronize` allowed, verbose logs, fake secrets |
| **Staging** | Production-like testing | Same setup as production, separate data and secrets |
| **Production** | Real users | Migrations only, minimal logs, real secrets, HTTPS |

Deployment steps most platforms share:

1. **Build** an image or bundle in CI (run lint and tests first).
2. **Provide configuration** as environment variables or secrets from the platform.
3. **Run migrations** before starting the new version (a release step or init job).
4. **Start** the new version and **check health** (22.4).
5. **Route traffic** to it, ideally with a rolling or blue-green rollout so there is no downtime.

A simple CI pipeline (GitHub Actions example):

```yaml
name: CI
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

Put **HTTPS** in front of the app using a reverse proxy or load balancer (Nginx, Caddy, a cloud load balancer). If you do, tell Express to trust the proxy so client IPs and protocols are read correctly:

```ts
const app = await NestFactory.create<NestExpressApplication>(AppModule);
app.set('trust proxy', 1);
```

(`NestExpressApplication` is imported from `@nestjs/platform-express`.)

---

## 22.4 Monitoring and health checks

### Health checks

Platforms like Kubernetes, ECS, and load balancers call a **health endpoint** to know whether your app can