[Previous](./[2]-Installing-Docker.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[4]-Running-And-Managing-Containers.md)

*Working With Docker*

# Lesson 3 - Docker Images And Dockerfiles

## 3.1 What Goes Into an Image

A Docker image is built up in layers, where each layer represents one instruction from a `Dockerfile` (a plain text file that describes how to assemble the image). Layers are stacked on top of a **base image** — usually a minimal operating system or runtime, such as `ubuntu` or `node`.

Because layers are cached, Docker only rebuilds the layers that actually changed, which makes repeated builds fast as long as the Dockerfile is written with caching in mind (see 3.3).

## 3.2 Writing a Dockerfile

A `Dockerfile` is just a text file, conventionally named `Dockerfile`, placed in the root of your project. Here's a minimal example for a small Node.js app:

```dockerfile
# Start from an official base image
FROM node:20-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy dependency files first, then install
COPY package.json package-lock.json ./
RUN npm install

# Copy the rest of the application code
COPY . .

# Document which port the app listens on
EXPOSE 3000

# Command to run when the container starts
CMD ["node", "server.js"]
```

## 3.3 Common Dockerfile Instructions

| Instruction | Purpose |
|---|---|
| `FROM` | Sets the base image everything else builds on |
| `WORKDIR` | Sets the working directory for subsequent instructions |
| `COPY` | Copies files from your machine into the image |
| `RUN` | Executes a command while building the image (e.g. installing packages) |
| `EXPOSE` | Documents which port the container listens on |
| `ENV` | Sets an environment variable inside the container |
| `CMD` | Specifies the default command run when a container starts |

> 💡 **Tip:** Copying dependency files (like `package.json`) and installing them *before* copying the rest of your source code — as shown above — means Docker can reuse the cached "install" layer whenever only your application code changes, not your dependencies. This alone can cut build times dramatically.

## 3.4 Building an Image

Once you have a Dockerfile, build an image with:

```bash
# -t tags (names) the image; the trailing dot means "build from this directory"
docker build -t my-app:1.0 .
```

- `my-app` is the image's name (repository).
- `1.0` is the image's tag (version) — if omitted, Docker defaults to `latest`.

You can view all images stored locally with:

```bash
docker images
```

And share an image by pushing it to a registry like Docker Hub:

```bash
docker tag my-app:1.0 yourusername/my-app:1.0
docker push yourusername/my-app:1.0
```

### Quick check: which instruction?

| Goal | Instruction |
|---|---|
| Pick the starting point for your image | `FROM` |
| Run `npm install` during the build | `RUN` |
| Set the command the container runs on startup | `CMD` |
| Copy your source code into the image | `COPY` |

With an image built, the next lesson covers actually running it — and the many flags that control how a container behaves.

---

[Previous](./[2]-Installing-Docker.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[4]-Running-And-Managing-Containers.md)
