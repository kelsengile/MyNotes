[Previous](./[2]-Compiling-and-Running-TypeScript.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[4]-TypeScript-in-the-Editor.md)

*Getting Started*

# Lesson 3 - tsconfig.json Fundamentals

## 3.1 What tsconfig.json Does

`tsconfig.json` sits at the root of a project and tells the TypeScript compiler which files to include, how strictly to check types, and what kind of JavaScript to produce. When this file is present, running `tsc` with no arguments compiles the whole project according to its rules.

Generate a starter file with:

```bash
npx tsc --init
```

---

## 3.2 The `compilerOptions` Object

This is where most configuration lives. A few of the most important options:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

- **`target`** — which version of JavaScript to compile down to (e.g. `ES2020`, `ES5`).
- **`module`** — the module system used in the output (`CommonJS`, `ESNext`, etc.).
- **`outDir`** / **`rootDir`** — where compiled files go, and where source files live.
- **`strict`** — turns on the full family of strict type-checking rules (highly recommended; see Lesson 52).
- **`esModuleInterop`** — smooths over differences between CommonJS and ES module imports.

---

## 3.3 Including and Excluding Files

You can control exactly which files the compiler looks at:

```json
{
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

`include` lists what to compile; `exclude` removes files from that set (it's applied after `include`). Test files and build output are commonly excluded.

---

## 3.4 Extending a Base Config

Larger projects, or monorepos, often share settings across multiple `tsconfig.json` files using `extends`:

```json
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist"
  }
}
```

This lets you define common rules once and override only what differs per package — a pattern explored further in Lesson 56.

---

## 3.5 A Minimal, Sensible Starting Config

For a typical Node.js or frontend project just getting started, this is a reasonable baseline:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

You'll revisit and expand this file often as your project grows — it's the single most important configuration file in a TypeScript codebase.

---

[Previous](./[2]-Compiling-and-Running-TypeScript.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[4]-TypeScript-in-the-Editor.md)
