[Previous](./[27]-Environment-Variables.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[29]-Domains-DNS-and-HTTPS.md)

*Deployment & Production*

# Lesson 28 - Hosting & Deploying a Website

## 28.1 What "Deploying" Means

**Deploying** is the process of taking your project's code and making it accessible to real users on the internet, running on a server or platform outside your own computer. This ranges from copying a few static files to a host, to a full pipeline that builds, tests, and releases a complex full-stack application.

---

## 28.2 Static Hosting

For static sites (plain HTML/CSS/JS, or a front end's build output), platforms like **Netlify**, **Vercel**, and **GitHub Pages** host files on a global **CDN (Content Delivery Network)**, serving them from servers geographically close to each visitor for speed. Deployment is often as simple as connecting a GitHub repository and letting the platform build and publish automatically on every push.

---

## 28.3 Hosting a Backend Server

A Node.js/Express server (Lesson 21) needs a platform that can actually run a persistent process, not just serve files. Options include:

- **Platform-as-a-Service (PaaS)** like Render, Railway, or Heroku — push code, the platform handles infrastructure
- **Serverless functions** (Vercel Functions, AWS Lambda) — code runs on-demand per request instead of as an always-on process
- **Virtual machines / containers** (AWS EC2, DigitalOcean Droplets, Docker) — full control, more setup responsibility

---

## 28.4 Containers, Briefly

**Docker** packages an application together with everything it needs to run (dependencies, runtime, configuration) into a **container** — a consistent, portable unit that behaves the same on a developer's laptop and in production, avoiding "it works on my machine" problems:

```dockerfile
FROM node:20
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "server.js"]
```

---

## 28.5 The Build-and-Deploy Sequence

A typical deployment: install dependencies → run the build step (Lesson 13) → run tests (Lesson 16) → upload/start the built application → verify it's healthy. Doing this manually every time is error-prone, which is exactly the problem CI/CD (Lesson 30) automates.

---

[Previous](./[27]-Environment-Variables.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[29]-Domains-DNS-and-HTTPS.md)
