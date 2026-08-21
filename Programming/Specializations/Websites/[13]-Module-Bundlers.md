[Previous](./[12]-Package-Managers.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[14]-Version-Control-with-Git.md)

*Tooling & Build Systems*

# Lesson 13 - Module Bundlers (Vite, Webpack)

## 13.1 The Problem Bundlers Solve

A real project might import dozens or hundreds of modules (Lesson 6), pull in npm packages, use TypeScript, and reference images and CSS from JavaScript. Browsers can't efficiently load hundreds of separate files (too many requests) and can't understand TypeScript or SCSS at all. A **bundler** resolves every import, transforms non-standard syntax into plain JS/CSS, and packages everything into a small number of optimized output files the browser can load quickly.

---

## 13.2 What Bundling Actually Does

At a high level, a bundler:

1. Starts from an entry file (e.g. `src/main.js`)
2. Follows every `import` statement to build a dependency graph
3. Transforms files as needed (TypeScript → JS, SCSS → CSS)
4. Combines everything into output bundles, often splitting large apps into multiple chunks loaded on demand
5. Minifies the result (removes whitespace, shortens variable names) to reduce file size

---

## 13.3 Vite

**Vite** is a modern build tool that serves source files directly over native ESM during development (no bundling needed at that stage, so the dev server starts almost instantly) and uses **Rollup** under the hood to produce an optimized bundle for production:

```bash
npm create vite@latest my-app
npm run dev     # instant dev server
npm run build   # optimized production bundle
```

Its speed and simple defaults have made it the most common starting point for new front-end projects.

---

## 13.4 Webpack

**Webpack** is an older, highly configurable bundler that bundles for both development and production. It introduced much of the vocabulary the ecosystem still uses — loaders (transform files), plugins (extend the build), and code splitting. Many established projects still run on Webpack, and understanding its configuration file (`webpack.config.js`) is useful for working in legacy codebases, even as new projects increasingly default to Vite.

---

## 13.5 Hot Module Replacement

Both tools support **Hot Module Replacement (HMR)** — during development, when you save a file, only the changed module is swapped in the running app, preserving current state (like form input or scroll position) instead of doing a full page reload. This tight feedback loop is one of the biggest quality-of-life improvements modern tooling brought to front-end development.

---

[Previous](./[12]-Package-Managers.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[14]-Version-Control-with-Git.md)
