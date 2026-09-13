[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)

# pnpm

pnpm is a fast, disk-space-efficient package manager for JavaScript. It reads and writes the same `package.json` format as npm and Yarn, but installs dependencies differently under the hood to save space and enforce stricter dependency rules.

Download [https://pnpm.io/installation](https://pnpm.io/installation)

---

## What Is pnpm?

npm and Yarn each copy a package's files into every project's `node_modules/` that needs it — even if ten projects use the exact same version. pnpm instead stores one copy in a global content-addressable store, and links to it from each project, saving significant disk space across many projects.

---

## Core Commands

```bash
pnpm init                    # Create a new package.json
pnpm add <package>           # Install a package and add it to package.json
pnpm add -D <package>        # Install a development-only dependency
pnpm remove <package>        # Remove a package
pnpm install                 # Install everything listed in package.json
pnpm update                  # Update dependencies to newer allowed versions
pnpm run <script>            # Run a script defined in package.json
```

---

## Stricter node_modules Structure

npm historically "hoisted" all dependencies to the top level of `node_modules/`, which let code accidentally import packages it never declared as a dependency. pnpm's linked structure prevents this — a project can only import what it actually lists in `package.json`, catching missing-dependency bugs earlier.

---

## pnpm vs. npm vs. Yarn

All three read the same `package.json` and pull from the same npm registry. pnpm is generally the fastest and most disk-efficient of the three, particularly valuable if you work across many JavaScript projects on one machine; npm remains the default with the widest compatibility.

---

## Example Walkthrough

```bash
pnpm init
pnpm add express
pnpm add jest -D
pnpm install
```

Creates a new project, adds Express as a dependency, adds Jest as a dev dependency, then installs everything.

[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)
