[Previous](./[22]-Cloud-Native-IaC-Tools.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[24]-Container-Registries.md)

*Containers & Orchestration*

# Lesson 23 - Docker Fundamentals

## 23.1 Images and Containers

**Docker** is the most widely used tool for building and running containers (introduced conceptually in Lesson 8). An **image** is a read-only, layered template containing an application's code, dependencies, and configuration. A **container** is a running instance of an image — you can run many containers from the same image, each isolated from the others. Images are built in layers, where each instruction adds a layer on top of the previous one, and Docker caches unchanged layers to speed up rebuilds.

---

## 23.2 Dockerfile Basics

A **Dockerfile** is a text file of instructions describing how to build an image:

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

Build and run it with:

```bash
docker build -t my-app:1.0 .
docker run -p 3000:3000 my-app:1.0
```

`FROM` sets the base image, `COPY` brings files into the image, `RUN` executes commands at build time, `EXPOSE` documents the port the app listens on, and `CMD` defines the command to run when the container starts.

---

## 23.3 Docker Compose

**Docker Compose** lets you define and run multi-container applications (e.g. a web app plus a database plus a cache) using a single YAML file, useful for local development and simple deployments:

```yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: example
```

Running `docker compose up` starts all defined services together, wired into a shared network so they can reach each other by service name (e.g. the `web` service connects to the database at host `db`). Compose is ideal for local development; for production-scale, multi-machine deployments, an orchestrator like Kubernetes (Lesson 25) is typically used instead.

---

[Previous](./[22]-Cloud-Native-IaC-Tools.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[24]-Container-Registries.md)
