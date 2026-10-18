[Previous](./[18]-Security-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[20]-Next-Steps-With-Nodejs.md)

*Quality And Delivery*

# Lesson 19 - Deployment

Deployment means putting your application somewhere it can run for real users. This lesson covers keeping processes alive, packaging with Docker, serverless options, and basic monitoring.

## 19.1 Production process managers

If you run `node app.js` and the app crashes or you close the terminal, it stops. A **process manager** starts your app, restarts it when it crashes, and keeps it running in the background.

**PM2** is a popular option:

```bash
npm install -g pm2

pm2 start app.js --name my-app       # start
pm2 start app.js -i max              # one instance per CPU core (cluster mode)
pm2 list                             # show running apps
pm2 logs my-app                      # view logs
pm2 restart my-app
pm2 stop my-app
pm2 startup                          # start on server boot
pm2 save                             # remember the current process list
```

You can describe your apps in `ecosystem.config.js`:

```js
module.exports = {
  apps: [{
    name: "my-app",
    script: "./app.js",
    instances: "max",
    env_production: { NODE_ENV: "production", PORT: 3000 },
  }],
};
```

Alternatives: **systemd** services on Linux, or letting a platform or container orchestrator handle restarts.

Production checklist:

- Set `NODE_ENV=production`.
- Install only production dependencies: `npm ci --omit=dev`.
- Handle `SIGTERM` for graceful shutdown (Lesson 8) so in-flight requests finish.
- Put a **reverse proxy** such as Nginx or Caddy in front for HTTPS, compression, and routing.

---

## 19.2 Docker containers

**Docker** packages your app and its environment into an **image** that runs the same everywhere.

Example `Dockerfile`:

```dockerfile
FROM node:22-alpine

WORKDIR /app

# Copy dependency files first so Docker can cache this step
COPY package*.json ./
RUN npm ci --omit=dev

COPY . .

ENV NODE_ENV=production
EXPOSE 3000

# Run as the non-root user provided by the image
USER node

CMD ["node", "app.js"]
```

A `.dockerignore` file keeps unneeded files out of the image:

```
node_modules
.git
.env
npm-debug.log
```

Build and run:

```bash
docker build -t my-app .
docker run -p 3000:3000 --env-file .env my-app
```

Tips:

- Use a specific Node.js version tag, not `latest`.
- Never put secrets inside the image. Pass them at runtime.
- Use `CMD ["node", "app.js"]` rather than `npm start`, so signals like `SIGTERM` reach Node.js directly.
- Use **multi-stage builds** to keep images small when you have a build step (such as TypeScript compilation).

---

## 19.3 Serverless options

With **serverless**, you upload functions and the platform runs them on demand. You do not manage servers, and you often pay only for actual usage.

Popular platforms: AWS Lambda, Google Cloud Functions, Azure Functions, Vercel, Netlify, and Cloudflare Workers.

A typical AWS Lambda handler:

```js
export const handler = async (event) => {
  return {
    statusCode: 200,
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ message: "Hello from Lambda" }),
  };
};
```

Comparison:

| | Traditional server / container | Serverless |
|---|---|---|
| Management | You handle servers and scaling | Platform handles it |
| Cost | Pay for uptime | Pay per request |
| State | Memory persists between requests | Functions are stateless |
| Startup | Always running | **Cold starts** add delay |
| Limits | Few | Time and memory limits per call |

Design notes for serverless:

- Keep functions small and fast to start.
- Do not rely on memory or local files between calls. Use databases and storage services.
- Create database clients **outside** the handler so warm calls reuse them, and watch connection limits, since many functions can open many connections at once.
- Long-running work and persistent WebSocket connections are a poor fit.

There are also **platform-as-a-service** options (Render, Railway, Fly.io, Heroku) where you push your code or container and they run it.

---

## 19.4 Monitoring basics

Once deployed, you need to know when something goes wrong, ideally before users tell you.

**Health checks:** a simple endpoint that load balancers and orchestrators can poll.

```js
app.get("/health", (req, res) => {
  res.status(200).json({ status: "ok", uptime: process.uptime() });
});
```

With plain `http`:

```js
if (req.url === "/health") {
  res.writeHead(200, { "Content-Type": "application/json" });
  return res.end(JSON.stringify({ status: "ok" }));
}
```

**What to monitor:**

| Area | Examples |
|---|---|
| Availability | Is the app up? Uptime checks |
| Errors | Error rate, crash count |
| Performance | Response time, requests per second |
| Resources | CPU, memory, event loop delay, open connections |
| Business | Signups, orders, payments |

**Tools:**

- **Logs:** collect structured logs (Lesson 16) in a central place.
- **Error tracking:** Sentry and similar services capture exceptions with context.
- **Metrics and dashboards:** Prometheus with Grafana, Datadog, or New Relic.
- **Uptime monitors:** services that ping your app from outside.

Useful Node.js numbers from inside the process:

```js
console.log(process.memoryUsage());
console.log(process.uptime());
console.log(process.cpuUsage());
```

Set up **alerts** for the important signals, such as high error rates or the app being unreachable, and keep a plan for rolling back to the previous version if a release goes wrong.

[Previous](./[18]-Security-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[20]-Next-Steps-With-Nodejs.md)