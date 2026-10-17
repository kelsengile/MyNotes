[Previous](./[16]-Testing.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[18]-Performance-And-Scaling.md)

*Quality And Delivery*

# Lesson 17 - Building And Deployment

Code on your laptop helps nobody until it runs somewhere users can reach it. The path from source code to a live application involves **building** it into a deployable form, running it in several **environments**, **hosting** it, and ideally **automating** all of that. This lesson covers each step.

## 17.1 Build Tools and Bundlers

The code you write is not always the code that runs. A **build** transforms your source code into the optimized form that is actually delivered.

### What a build step does

| Step | What it means | Why |
|---|---|---|
| **Transpiling / compiling** | Converting code the target cannot run directly into code it can (TypeScript or JSX to JavaScript; Java and C# to compiled output; Dart to native code) | Browsers and servers run a specific form of code |
| **Bundling** | Combining many source files and packages into a few files | Fewer network requests; dependencies resolved |
| **Minification** | Removing whitespace and shortening names | Smaller files download faster |
| **Tree-shaking** | Dropping code that is never used | Smaller output |
| **Code splitting** | Cutting the output into chunks loaded only when needed | Faster first load (Lesson 18) |
| **Asset processing** | Optimizing images and compiling styles; adding content hashes to filenames so browsers can cache them safely | Performance and correct cache updates |

### Common tools

| Tool | Role |
|---|---|
| **Vite** | A fast dev server and bundler widely used for React, Vue.js, and Svelte |
| **webpack** | A long-established, highly configurable bundler |
| **esbuild**, **Rollup**, **Parcel** | Other bundlers, some used internally by other tools |
| **Turbopack** | The bundler integrated with Next.js |
| **Angular CLI** | Wraps its own build tooling behind `ng build` |
| **Maven / Gradle** | Build Java projects into packaged applications |
| **`dotnet publish`** | Builds and packages ASP.NET Core applications |
| **`flutter build`** | Builds a Flutter app for Android, iOS, web, or desktop |

Most frameworks hide these tools behind a single command, so you rarely configure them directly at first:

| Framework | Build command (typical) | Output |
|---|---|---|
| Vite-based (React, Vue.js, Svelte) | `npm run build` | A `dist/` folder of static files |
| Next.js | `npm run build` | A `.next/` folder, run with `npm start` |
| Angular | `ng build` | A `dist/` folder |
| Spring Boot | `./mvnw package` or `./gradlew build` | A runnable `.jar` file |
| ASP.NET Core | `dotnet publish -c Release` | A folder of published binaries |
| Flutter | `flutter build apk` or `flutter build web` | An Android package or a web folder |

Some languages, such as Python and PHP, usually have no compile step, but you may still package your app, for example into a container (17.3).

### Development vs production builds

| | Development | Production |
|---|---|---|
| **Goal** | Fast feedback | Small and fast for users |
| **Minified** | No | Yes |
| **Helpful error messages** | Yes | Reduced |
| **Source maps** | Yes | Optional |
| **Command** | `npm run dev` | `npm run build` |

Build output is **generated**, not hand-written. It is listed in `.gitignore` and recreated whenever needed (Lesson 4).

---

## 17.2 Environments (Development, Staging, Production)

An **environment** is a complete place where your application runs, with its own settings, data, and servers. Most teams use at least three.

| Environment | Who uses it | Purpose | Data |
|---|---|---|---|
| **Development** | Developers, on their own machines | Building and trying things out | Fake or sample data |
| **Staging** (or test) | The team, and sometimes testers or clients | A near-copy of production for final checks | Fake or anonymized data |
| **Production** | Real users | The live application | Real data |

```text
Developer's laptop ──► Staging ──► Production
   (build & try)     (final checks)    (live)
```

### Why separate environments?

- You can test risky changes **without affecting real users**.
- Staging catches problems that only appear on real infrastructure, such as differing versions or missing settings.
- **Real user data stays protected**: developers never need access to production data for everyday work.

### What differs between environments

| Setting | Development | Production |
|---|---|---|
| **Debug mode** | On | **Off** |
| **Database** | Local or temporary | Real, backed up |
| **API keys** | Test keys | Live keys |
| **Logging level** | Detailed | Focused on warnings and errors |
| **Error pages** | Detailed for developers | Generic for users |
| **HTTPS** | Optional locally | Required |

### One build, many configurations

A widely followed principle, described in the "Twelve-Factor App" methodology (12factor.net), is to build the application **once** and change its behavior only through **configuration**, using environment variables (Lesson 7). That way, what you tested in staging is the *same code* that goes to production, with only the settings changed.

### Feature flags

A **feature flag** is a setting that switches a feature on or off without redeploying. It lets teams ship unfinished work safely hidden, release to a small group first, and switch something off quickly if it misbehaves.

> **Rules of thumb:** never test on production data, never share secrets between environments, and keep staging as similar to production as you can.

---

## 17.3 Hosting and Deployment Options

**Deployment** is the act of putting a built application somewhere it can run and be reached. The right choice depends on what you built.

### Static hosting and CDNs

If your build output is just files (HTML, CSS, JavaScript, images), as with a React, Vue.js, Svelte, or Angular single-page app or a statically generated site, you can host it on a **static hosting** service backed by a **CDN (content delivery network)**. A CDN stores copies of your files on servers around the world and serves each visitor from a nearby one, so pages load quickly. It is typically the simplest, cheapest, and most scalable option. Examples include Netlify, Vercel, Cloudflare Pages, and GitHub Pages.

### Platform as a Service (PaaS)

A **PaaS** runs your server application for you: you push your code, and the platform builds it, starts it, and handles much of the infrastructure. It is a great way to get a backend online with little operations work. Examples include Render, Railway, Fly.io, and Heroku, and the managed application services of the large cloud providers.

### Containers

A **container** packages your application together with everything it needs (runtime, libraries, settings) into one portable unit, so it runs identically everywhere. **Docker** is the best-known tool. A container is described by a *Dockerfile*:

```dockerfile
# Use a currently supported LTS version of Node.js
FROM node:24-alpine

WORKDIR /app

# Copy the dependency files first so Docker can cache this layer
COPY package*.json ./
RUN npm ci --omit=dev

# Then copy the rest of the source code
COPY . .

EXPOSE 3000
CMD ["node", "server.js"]
```

Containers can be run on a single machine, on PaaS platforms, or at large scale with an orchestrator such as **Kubernetes**.

### Serverless

In **serverless** (or functions-as-a-service) hosting, you deploy individual functions that run only when triggered and are billed per use, with no servers to manage. It is a good fit for spiky or occasional workloads, and some meta-frameworks deploy parts of their apps this way automatically.

### Virtual servers (IaaS)

You can also rent a raw virtual machine from a cloud provider such as AWS, Azure, or Google Cloud, and set up everything yourself. This gives the most control and requires the most work: you manage the operating system, updates, security, and scaling.

### Comparing the options

| Option | You manage | Best for |
|---|---|---|
| **Static hosting / CDN** | Almost nothing | Frontends and static sites |
| **PaaS** | Your code and settings | Most backends and full-stack apps |
| **Containers** | The container image | Portable, consistent deployments |
| **Serverless** | Individual functions | Event-driven or variable workloads |
| **Virtual servers (IaaS)** | Everything | Full control, special requirements |

### Running a backend in production

Do **not** run your framework's development server in production (Lesson 7). Instead, backends run behind production-grade components:

- A production **application server**: for example, Gunicorn or Uvicorn for Python, or the embedded server of a built Spring Boot `.jar`.
- Often a **reverse proxy** such as Nginx in front of it, which handles HTTPS, serves static files, and forwards requests to your application.
- A **process manager** (or the platform itself) that restarts the app if it crashes.

### Deploying mobile apps

Mobile apps are not hosted on a server. They are built into packages (an `.apk` or `.aab` for Android, an `.ipa` for iOS) and distributed through the **Apple App Store** and **Google Play**, after review. Updates must be published again through the stores, which is why mobile APIs need careful versioning (Lesson 12).

### Deployment strategies

| Strategy | How it works | Benefit |
|---|---|---|
| **Replace in place** | Stop the old version, start the new | Simple, but causes downtime |
| **Rolling** | Update servers one at a time | No downtime |
| **Blue-green** | Run the new version beside the old, then switch traffic over | Instant, easy rollback |
| **Canary** | Send a small share of users to the new version first | Catches problems with limited impact |

Whatever you choose, **make sure you can roll back** to the previous version quickly.

---

## 17.4 CI/CD Basics

Doing all of the above by hand is slow and error-prone. **CI/CD** automates it.

- **Continuous Integration (CI)**: every time code is pushed, an automated system builds it and runs the tests, so problems are found immediately and early.
- **Continuous Delivery (CD)**: code that passes is automatically prepared, and can be released with the click of a button.
- **Continuous Deployment** (also abbreviated CD): code that passes is released to production *automatically*, with no manual step.

### A typical pipeline

```text
 Push code ──► Install ──► Lint ──► Test ──► Build ──► Deploy
              dependencies  (style   (Lesson  (Lesson   (to staging,
                            checks)    16)      17.1)    then production)
```

If any stage fails, the pipeline stops, and the team is notified. The result is a **repeatable, trustworthy path to production**, instead of "it worked when I did it manually."

### Common CI/CD tools

| Tool | Notes |
|---|---|
| **GitHub Actions** | Built into GitHub; configured with YAML files in the repository |
| **GitLab CI/CD** | Built into GitLab |
| **Jenkins** | A long-standing, self-hosted automation server |
| **CircleCI** and others | Hosted CI services |

### An example workflow

This GitHub Actions workflow runs on every push and pull request. It installs dependencies, runs the tests, and builds the project for a Node.js-based app:

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
      - name: Check out the code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: lts/*
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

Reading it from top to bottom: when code is pushed to `main` or a pull request is opened, start a fresh Linux machine, fetch the code, install Node.js, install dependencies exactly as recorded in the lock file (`npm ci`, Lesson 5), run the tests, and run the build. Action versions (such as `@v4`) change over time, so check the current documentation when you write your own.

### Good CI/CD habits

1. **Keep the pipeline fast.** A slow pipeline gets ignored or bypassed.
2. **Treat a failing pipeline as the team's top priority.**
3. **Never put secrets in the workflow file.** Use the CI system's secret storage.
4. **Deploy the same built artifact** through staging and production (17.2).
5. **Automate rollbacks** or make them a single step.
6. **Start small.** Even a pipeline that only runs the tests on each push is a big improvement over none.

---

[Previous](./[16]-Testing.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[18]-Performance-And-Scaling.md)
