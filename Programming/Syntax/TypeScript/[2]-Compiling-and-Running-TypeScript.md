[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[3]-tsconfig-Fundamentals.md)

*Getting Started*

# Lesson 2 - Compiling And Running TypeScript (tsc, ts-node, tsx)

## 2.1 The Core Idea: TypeScript Doesn't Run Directly

Browsers and Node.js only understand JavaScript — they have no idea what a `: string` annotation means. TypeScript code must first be **compiled** (also called "transpiled") into plain JavaScript before it can run. There are a few common ways to do this, each suited to different workflows.

---

## 2.2 Using `tsc` (the TypeScript Compiler)

`tsc` is the official compiler. It reads `.ts` files and emits `.js` files.

```bash
npx tsc index.ts        # compiles a single file
npx tsc                 # compiles the whole project, using tsconfig.json
npx tsc --watch         # recompiles automatically on every save
```

`tsc` is the most reliable option for production builds because it performs full type checking and produces the exact output your `tsconfig.json` describes.

---

## 2.3 Using `ts-node` for Instant Execution

Running `tsc` then `node` every time you change a file is slow during development. `ts-node` compiles and runs TypeScript in a single step, entirely in memory.

```bash
npm install --save-dev ts-node
npx ts-node index.ts
```

This is useful for scripts, quick experiments, and backend development where you don't want a separate build step for every change.

---

## 2.4 Using `tsx` for Fast, Modern Execution

`tsx` is a newer, faster alternative to `ts-node`, built on `esbuild`. It supports modern syntax out of the box, handles ESM/CommonJS interop smoothly, and starts up significantly faster.

```bash
npm install --save-dev tsx
npx tsx index.ts

# Watch mode
npx tsx watch index.ts
```

Many teams now prefer `tsx` for local development, reserving `tsc` purely for type-checking and production builds.

---

## 2.5 Choosing the Right Tool

| Tool | Best for | Type-checks? |
|---|---|---|
| `tsc` | Production builds, CI, type checking | Yes |
| `ts-node` | Quick scripts, Node backends | Yes (slower) |
| `tsx` | Fast local development | No (speed-focused) |

A common real-world setup uses `tsx` (or `ts-node`) during development, and `tsc` as a final type-checking and build step before deployment.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[3]-tsconfig-Fundamentals.md)