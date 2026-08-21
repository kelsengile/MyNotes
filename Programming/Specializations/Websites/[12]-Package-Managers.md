[Previous](./[11]-CSS-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[13]-Module-Bundlers.md)

*Tooling & Build Systems*

# Lesson 12 - Package Managers (npm, yarn)

## 12.1 What a Package Manager Does

Almost no modern web project is written entirely from scratch — projects depend on **packages** (reusable code published by others). A **package manager** installs those packages, tracks which versions your project needs, and manages the tree of dependencies those packages themselves rely on. **npm** (Node Package Manager) ships with Node.js and is the default; **yarn** and **pnpm** are popular alternatives that solve the same problem with different performance and disk-usage tradeoffs.

---

## 12.2 package.json

Every npm project has a `package.json` file describing it:

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js",
    "build": "vite build"
  },
  "dependencies": {
    "express": "^4.19.0"
  },
  "devDependencies": {
    "vite": "^5.0.0"
  }
}
```

`dependencies` are needed to run the app (e.g. a web server library); `devDependencies` are only needed during development (e.g. a bundler or test runner) and aren't shipped to production.

---

## 12.3 Installing Packages

```bash
npm install express       # adds to dependencies
npm install --save-dev vite   # adds to devDependencies
npm install                   # installs everything already listed in package.json
```

Installed packages are downloaded into a `node_modules` folder, which is never committed to version control (Lesson 14) — it's regenerated from `package.json` whenever needed.

---

## 12.4 Semantic Versioning and Lockfiles

Package versions follow **semantic versioning**: `MAJOR.MINOR.PATCH` (e.g. `4.19.2`). A `^` prefix (`^4.19.0`) allows compatible updates (new minor/patch versions); a `~` prefix allows only patch updates. Because version ranges can resolve differently over time, package managers also generate a **lockfile** (`package-lock.json` for npm, `yarn.lock` for yarn) that pins the *exact* resolved versions installed, so every developer and every deployment gets identical dependency trees.

---

## 12.5 Running Scripts

The `scripts` section of `package.json` defines shortcuts for common tasks:

```bash
npm run build
npm start   # "start" and "test" can drop the "run"
```

This is how most projects standardize commands like starting a dev server, running tests, or building for production — regardless of what tool actually powers each step underneath.

---

[Previous](./[11]-CSS-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[13]-Module-Bundlers.md)
