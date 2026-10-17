[Previous](./[14]-Real-Time-And-Advanced-Features.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[16]-Next-Steps-With-Express.md)

*Hardening And Delivery*

# Lesson 15 - Deployment

Deployment means moving your app from your laptop to a server where other people can use it. This lesson covers configuring the app for different environments, keeping it running with PM2, packaging it with Docker, and putting a reverse proxy with HTTPS in front of it.

---

## 15.1 Environment configuration

The same code runs in several **environments**: your laptop (development), an automated test run (test), and the live server (production). Each needs different settings, such as database addresses, log levels, and secrets. Keep the **code identical** and change only the **configuration**.

### NODE_ENV

`NODE_ENV` is the standard variable that tells Node.js and many libraries which environment they are in.

| Value | Meaning |
|---|---|
| `development` | Detailed errors, pretty logs |
| `test` | Set automatically by test runners (Lesson 13) |
| `production` | Faster behavior, caching, minimal error details |

**Always set `NODE_ENV=production` on live servers.** Express caches view templates and generates less verbose error output in production, and many libraries skip expensive development checks.

### Environment variables

Settings that change between environments (or are secret) come from environment variables, read in **one central config module** as shown in Lesson 5.

```
PORT=3000
NODE_ENV=production
DATABASE_URL=postgres://user:pass@db.internal:5432/app
JWT_SECRET=a-long-random-string
REDIS_URL=redis://redis.internal:6379
```

Generate a strong secret with:

```bash
node -e "console.log(require('node:crypto').randomBytes(48).toString('hex'))"
```

### Where the values come from

| Environment | How to provide variables |
|---|---|
| Local development | A `.env` file (never committed) |
| Docker | `-e` flags, `--env-file`, or `environment:` in Compose |
| PM2 | The `env` section of the ecosystem file |
| Hosting platforms (Render, Railway, Fly.io, and similar) | The platform's dashboard or CLI |
| Cloud servers | The platform's secrets manager, or the process manager's configuration |

**Rules:**

- Never commit secrets to Git, and never bake them into a Docker image.
- Use different secrets in each environment. A leaked development secret must not unlock production.
- Fail fast: the config module throws at startup if a required variable is missing, so a broken deploy shows up immediately instead of at the first request.
- Keep `.env.example` in Git as documentation (Lesson 5).

### A pre-deployment checklist

- `NODE_ENV=production`
- Secrets are long, random, and not in the repository
- Database credentials use a user with limited permissions
- Helmet, CORS, and rate limiting are enabled (Lesson 11)
- Error responses do not expose stack traces (Lesson 7)
- Logs go to the console in a structured format (Lesson 12)
- Health endpoints exist (Lesson 12)
- `npm audit` shows no serious unresolved issues
- Production dependencies only: install with `npm ci --omit=dev`

### Install exactly what you tested

On servers use `npm ci` instead of `npm install`. It installs exactly the versions in `package-lock.json` and fails if the lock file and `package.json` disagree, giving repeatable builds. Commit `package-lock.json`.

### Graceful shutdown

When a deployment restarts the app, in-flight requests should finish first. Handle the stop signals (the same pattern as Lesson 8):

```js
const server = app.listen(config.port);

function shutdown(signal) {
  console.log(`${signal} received: closing server`);

  server.close(async () => {           // stops new connections, waits for current requests
    await closeDatabase();             // for example prisma.$disconnect()
    process.exit(0);
  });

  setTimeout(() => process.exit(1), 10_000).unref();   // force exit if it takes too long
}

process.on('SIGTERM', () => shutdown('SIGTERM'));   // sent by Docker, PM2, and Kubernetes
process.on('SIGINT', () => shutdown('SIGINT'));     // Ctrl+C
```

---

## 15.2 PM2 and process management

If you run `node server.js` in a terminal, the app stops when you close the terminal, and it stays dead if it crashes. A **process manager** keeps it running, restarts it after crashes, starts it at boot, and manages logs. **PM2** is the most common one for Node.js.

```bash
npm install -g pm2
```

### Basic commands

```bash
pm2 start src/server.js --name my-api     # start in the background
pm2 list                                  # show running processes
pm2 logs my-api                           # stream logs
pm2 monit                                 # live CPU and memory dashboard
pm2 restart my-api                        # restart
pm2 reload my-api                         # zero-downtime restart (cluster mode)
pm2 stop my-api
pm2 delete my-api
```

### The ecosystem file

Put configuration in a file so it is repeatable and can be committed. PM2 loads `ecosystem.config.cjs` (the `.cjs` extension is required when your `package.json` uses `"type": "module"`):

```js
// ecosystem.config.cjs
module.exports = {
  apps: [
    {
      name: 'my-api',
      script: './src/server.js',
      instances: 'max',            // one process per CPU core
      exec_mode: 'cluster',        // share the port across processes
      max_memory_restart: '500M',  // restart if a process grows past 500 MB
      env: {
        NODE_ENV: 'development',
      },
      env_production: {
        NODE_ENV: 'production',
        PORT: 3000,
      },
    },
    {
      name: 'my-worker',           // the BullMQ worker from Lesson 14
      script: './src/worker.js',
      instances: 1,
      env_production: { NODE_ENV: 'production' },
    },
  ],
};
```

```bash
pm2 start ecosystem.config.cjs --env production
```

### Why cluster mode?

Node.js runs your JavaScript on **one thread**, so a single process uses only one CPU core. Cluster mode starts several copies that share one port, using all cores and giving you `reload` without downtime.

**Consequence:** your app must be **stateless**. Data kept in a variable lives in only one process, and the next request may reach a different one. Keep sessions, caches, rate limit counters, and socket state in shared stores such as Redis or the database (Lessons 9, 11, and 14).

### Surviving server reboots

```bash
pm2 startup        # prints a command to run, which registers PM2 with the system's init
pm2 save           # saves the current process list so it is restored at boot
```

### Logs

PM2 writes logs to `~/.pm2/logs/`. Install the rotation module so they do not fill the disk:

```bash
pm2 install pm2-logrotate
```

### Alternatives

| Option | Notes |
|---|---|
| `systemd` | The Linux built-in. No extra install, good for simple single processes |
| Docker with a restart policy | `--restart unless-stopped` (next section) |
| Kubernetes | Restarts and scales containers for you |
| Managed platforms | Handle restarts for you, so PM2 is often unnecessary there |

If you deploy with Docker or a managed platform, **the platform replaces PM2**. Do not stack them without a reason.

---

## 15.3 Docker

**Docker** packages your app, its dependencies, and its runtime into an **image** that runs the same way everywhere. This ends "it works on my machine". A running instance of an image is a **container**.

### The Dockerfile

A `Dockerfile` is the recipe for building the image. A **multi-stage** build keeps the final image small by leaving build tools behind.

```dockerfile
# ---- Stage 1: install production dependencies ----
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev

# ---- Stage 2: final runtime image ----
FROM node:22-alpine
WORKDIR /app

ENV NODE_ENV=production

COPY --from=deps /app/node_modules ./node_modules
COPY package.json ./
COPY src ./src

USER node                      # do not run as root
EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s \
  CMD node -e "fetch('http://localhost:3000/health').then(r => process.exit(r.ok ? 0 : 1)).catch(() => process.exit(1))"

CMD ["node", "src/server.js"]
```

| Instruction | Meaning |
|---|---|
| `FROM` | The base image to start from |
| `WORKDIR` | The working folder inside the image |
| `COPY` | Copy files from your project into the image |
| `RUN` | Execute a command while building |
| `ENV` | Set an environment variable |
| `USER` | Run as a non-root user, limiting damage if the app is compromised |
| `EXPOSE` | Documents the port the app listens on |
| `HEALTHCHECK` | A command Docker runs to see whether the app is healthy |
| `CMD` | The command run when the container starts |

**Copy `package.json` and the lock file before the source code.** Docker caches each step. Dependencies change rarely, so this order lets rebuilds skip `npm ci` when only your code changed.

> If your project uses TypeScript (Lesson 16), add a build stage that compiles to `dist/` and copy only `dist/` into the final image.

### The .dockerignore file

Keep unnecessary and secret files out of the build:

```
node_modules
npm-debug.log
.git
.gitignore
.env
.env.*
tests
coverage
Dockerfile
docker-compose.yml
```

### Build and run

```bash
docker build -t my-api .

docker run -d \
  --name my-api \
  -p 3000:3000 \
  --env-file .env.production \
  --restart unless-stopped \
  my-api
```

`-p 3000:3000` maps port 3000 on your machine to port 3000 in the container. Your app must listen on `0.0.0.0` or the default (all interfaces), not just `localhost`, or the mapping will not reach it. `app.listen(PORT)` already does the right thing.

Useful commands:

```bash
docker ps                      # running containers
docker logs -f my-api          # follow logs
docker exec -it my-api sh      # open a shell inside the container
docker stop my-api
```

### Docker Compose: the app and its services together

Real apps need a database and often Redis. **Docker Compose** describes them all in one file:

```yaml
# docker-compose.yml
services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production
      DATABASE_URL: postgres://app:secret@db:5432/app
      REDIS_URL: redis://redis:6379
      JWT_SECRET: ${JWT_SECRET}          # read from your shell or a .env file
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    restart: unless-stopped

  worker:
    build: .
    command: ["node", "src/worker.js"]
    environment:
      NODE_ENV: production
      REDIS_URL: redis://redis:6379
    depends_on:
      - redis
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: app
    volumes:
      - pgdata:/var/lib/postgresql/data     # keeps data when the container is replaced
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
      interval: 5s
      timeout: 3s
      retries: 10

  redis:
    image: redis:7-alpine

volumes:
  pgdata:
```

```bash
docker compose up -d --build     # build and start everything
docker compose logs -f api
docker compose down              # stop and remove containers (volumes stay)
```

Inside the Compose network, services reach each other **by service name**: the database host is `db`, not `localhost`.

### Docker rules of thumb

- **One process per container.** Run the worker as its own container, not inside the API's.
- **Containers are disposable.** Anything written to the container's filesystem disappears when it is replaced. Use volumes or external services for data and uploads (Lesson 10).
- **Never put secrets in the image or the Dockerfile.** Pass them at run time.
- **Pin versions** (`node:22-alpine`, not `node:latest`) so builds are repeatable.
- Run database migrations as a separate step before starting new containers, for example `npx prisma migrate deploy`.

---

## 15.4 Reverse proxies and HTTPS

You should not expose Node.js directly to the internet on port 80 or 443. Put a **reverse proxy** in front. It receives public traffic and forwards it to your app.

```
Internet → [ Nginx / Caddy: HTTPS, compression, limits ] → Express (port 3000)
```

### Why use one?

| Benefit | Explanation |
|---|---|
| **HTTPS termination** | The proxy handles certificates and encryption |
| **Ports 80 and 443** | Your app can run unprivileged on port 3000 |
| **Load balancing** | Spread traffic across several app instances |
| **Static files and compression** | Served efficiently without involving Node.js |
| **Request limits and buffering** | Protects the app from slow or oversized clients |
| **One entry point** | Several apps can share one server and one domain |

### Nginx

```nginx
# /etc/nginx/sites-available/my-api
upstream express_app {
    server 127.0.0.1:3000;
    keepalive 32;
}

server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://express_app;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Needed for WebSockets (Lesson 14)
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/my-api /etc/nginx/sites-enabled/
sudo nginx -t              # check the configuration
sudo systemctl reload nginx
```

For SSE routes (Lesson 14) add `proxy_buffering off;` in that location block.

### Caddy (the simplest option)

Caddy obtains and renews HTTPS certificates **automatically**. The whole configuration can be:

```
api.example.com {
    reverse_proxy localhost:3000
}
```

### HTTPS with Let's Encrypt on Nginx

**Let's Encrypt** issues free certificates. **Certbot** automates getting and renewing them:

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d api.example.com
```

Certbot edits your Nginx configuration to serve HTTPS and redirect HTTP to HTTPS, and sets up automatic renewal. Your domain's DNS must already point to the server.

### Tell Express it is behind a proxy

The proxy hides the real client details from Express. Without help, `req.ip` is the proxy's address and `req.protocol` is always `http`, which breaks rate limiting (Lesson 11) and `secure` cookies (Lesson 9). Enable `trust proxy`:

```js
app.set('trust proxy', 1);    // trust exactly one proxy in front of the app
```

Now Express reads `X-Forwarded-For` and `X-Forwarded-Proto`, so `req.ip`, `req.protocol`, and `req.secure` reflect the real client.

Set the number to the real count of proxies. Trusting more hops than exist allows clients to fake their IP with their own `X-Forwarded-For` header.

### Redirect HTTP to HTTPS

Usually the proxy does this. If you need it in Express (with `trust proxy` set):

```js
app.use((req, res, next) => {
  if (config.isProduction && !req.secure) {
    return res.redirect(308, `https://${req.get('host')}${req.originalUrl}`);
  }
  next();
});
```

### Load balancing several instances

```nginx
upstream express_app {
    least_conn;                    # send each request to the least busy server
    server 10.0.0.11:3000;
    server 10.0.0.12:3000;
    server 10.0.0.13:3000;
}
```

This is another reason the app must be stateless: any instance can receive any request.

### Deployment options at a glance

| Option | Good for | You manage |
|---|---|---|
| **Platform as a service** (Render, Railway, Fly.io, Heroku-style) | Quickest path, small and medium apps | Almost nothing: push code or an image |
| **VPS** (a rented Linux server) with Docker or PM2 plus Nginx | Full control, predictable cost | The server, updates, and security |
| **Container services** (AWS ECS, Google Cloud Run, Azure Container Apps) | Scaling container workloads | Images and configuration |
| **Kubernetes** | Large systems with many services | A lot. Choose it only when you need it |

### A minimal deployment flow

1. Push code to Git. CI runs tests (Lesson 13).
2. Build the Docker image and push it to a registry.
3. On the server (or platform), pull the new image.
4. Run database migrations.
5. Start the new containers, wait for `/ready` to succeed (Lesson 12), then stop the old ones.
6. Watch logs and metrics for errors. Keep the previous image so you can **roll back** quickly.

---

[Previous](./[14]-Real-Time-And-Advanced-Features.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[16]-Next-Steps-With-Express.md)
