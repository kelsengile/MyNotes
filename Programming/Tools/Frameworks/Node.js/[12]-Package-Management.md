[Previous](./[11]-Child-Processes-And-Worker-Threads.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[13]-Environment-And-Configuration.md)

*Tooling And Projects*

# Lesson 12 - Package Management

Almost every Node.js project depends on other people's code. This lesson explains the `package.json` file, how scripts and lock files work, the main package managers, and how version numbers are meant to be read.

## 12.1 package.json explained

`package.json` is the manifest of a project. Create one with `npm init -y`.

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "Example app",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js",
    "test": "node --test"
  },
  "dependencies": {
    "express": "^4.19.0"
  },
  "devDependencies": {
    "eslint": "^9.0.0"
  },
  "engines": {
    "node": ">=20"
  },
  "license": "MIT"
}
```

Important fields:

- **name / version:** identify the package (required to publish).
- **type:** `"module"` for ESM or `"commonjs"` (default).
- **main / exports:** the entry point other code imports.
- **scripts:** named commands.
- **dependencies:** packages needed at runtime.
- **devDependencies:** packages needed only for development.
- **engines:** which Node.js versions are supported.
- **private:** set to `true` to prevent accidental publishing.

---

## 12.2 Scripts and lock files

**Scripts** are shortcuts run with `npm run <name>`. `start` and `test` can be run without `run` (`npm start`, `npm test`).

```bash
npm run dev
npm test
npm start
```

Scripts can chain commands (`"build": "npm run clean && tsc"`), and local tools installed in `node_modules/.bin` can be used without a full path. Optional `pre` and `post` versions run automatically (`prebuild` runs before `build`).

A **lock file** (`package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`) records the **exact** version of every installed package, including indirect ones. This makes installs repeatable, so every developer and server gets identical code.

Rules:

- Commit the lock file to Git.
- Do not edit it by hand.
- In automated builds, use `npm ci`, which installs exactly what the lock file says and fails if it does not match `package.json`.

---

## 12.3 npm, yarn, and pnpm

All three install the same packages from the same registry. They differ in speed, disk use, and features.

| Tool | Comes with Node.js? | Notes |
|---|---|---|
| npm | Yes | The default and most widely documented |
| yarn | No (via Corepack) | Workspaces and plug-and-play mode |
| pnpm | No (via Corepack) | Fast and saves disk space by sharing packages |

Equivalent commands:

| Action | npm | yarn | pnpm |
|---|---|---|---|
| Install all | `npm install` | `yarn` | `pnpm install` |
| Add package | `npm install pkg` | `yarn add pkg` | `pnpm add pkg` |
| Add dev package | `npm install -D pkg` | `yarn add -D pkg` | `pnpm add -D pkg` |
| Remove | `npm uninstall pkg` | `yarn remove pkg` | `pnpm remove pkg` |
| Run script | `npm run dev` | `yarn dev` | `pnpm dev` |

Pick one tool per project and use only that one, so you do not end up with conflicting lock files.

---

## 12.4 Semantic versioning

Package versions follow **semantic versioning (semver)**: `MAJOR.MINOR.PATCH`, for example `4.19.2`.

- **MAJOR:** breaking changes. Your code may need updates.
- **MINOR:** new features that stay backward compatible.
- **PATCH:** bug fixes only.

Version ranges in `package.json`:

| Written | Meaning | Allows |
|---|---|---|
| `4.19.2` | Exact version | Only 4.19.2 |
| `^4.19.2` | Compatible (the default) | 4.19.2 up to below 5.0.0 |
| `~4.19.2` | Patch updates only | 4.19.2 up to below 4.20.0 |
| `*` or `latest` | Anything | Not recommended |

For versions below 1.0.0, a caret is stricter because anything may change: `^0.2.3` allows only `0.2.x`.

Useful commands:

```bash
npm outdated        # see which packages have newer versions
npm update          # update within allowed ranges
npm ls              # see the dependency tree
```

Read the changelog before upgrading a major version, and run your tests afterward.

[Previous](./[11]-Child-Processes-And-Worker-Threads.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[13]-Environment-And-Configuration.md)