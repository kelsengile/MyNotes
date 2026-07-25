# [11] Monorepos & Workspaces

## What Is a Monorepo?

A **monorepo** is a single repository that contains multiple, separately versioned packages — as opposed to a **polyrepo** setup, where each package lives in its own repository.

```
my-monorepo/
├── packages/
│   ├── ui-components/
│   │   └── package.json
│   ├── api-client/
│   │   └── package.json
│   └── shared-utils/
│       └── package.json
└── package.json          # root manifest
```

Large projects (and companies) often use monorepos so related packages can be developed, tested, and released together.

## What Are Workspaces?

A **workspace** is a package manager feature that understands "this repository contains multiple packages" and manages them together — installing shared dependencies once, and letting local packages reference each other directly without needing to publish to a registry first.

**npm workspaces** (`package.json` at the root):
```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": [
    "packages/*"
  ]
}
```

**Cargo workspaces** (`Cargo.toml` at the root):
```toml
[workspace]
members = [
    "packages/ui-components",
    "packages/api-client",
]
```

**Python** doesn't have a single standard equivalent, but tools like Poetry and `uv` support similar multi-package workspace setups.

## Benefits of Monorepos + Workspaces

- **Shared dependencies** — one `node_modules` (or equivalent) instead of duplicated installs per package
- **Local package linking** — package A can depend on package B by referencing it locally, without publishing B first, and changes to B are immediately visible to A
- **Atomic changes** — a single commit/PR can update multiple packages together, keeping them in sync
- **Consistent tooling** — one linter config, one CI pipeline, one set of conventions across all packages

## Trade-offs

- Repositories can get large, and tooling (git, CI) needs to scale accordingly
- Build and test times can grow if not carefully scoped (most workspace tools support running commands against only the packages that changed)
- Requires more upfront tooling investment than a single simple project

## Running Commands Across a Workspace

```bash
npm run build --workspaces          # run in every workspace package
npm run build --workspace=ui-components   # run in just one

cargo build --workspace              # build every crate in the workspace
```

## Publishing From a Monorepo

Each package in a monorepo still gets published independently, with its own version number — the monorepo is just how the *source code* is organized, not how it's distributed. Tools like Lerna, Nx, Turborepo, and Changesets (JavaScript ecosystem) help coordinate versioning and publishing across many packages at once.

## Try It Yourself

If you have two small related packages, try setting them up in a single repo with npm or Cargo workspaces, and have one depend on the other locally (no publishing required).

## Up Next

Next: **private and internal registries**, for packages you don't want to publish publicly.

---
⬅ [10] [Publishing Packages](./%5B10%5D-Publishing-Packages.md) | ➡ [12] [Private & Internal Registries](./%5B12%5D-Private-and-Internal-Registries.md)
