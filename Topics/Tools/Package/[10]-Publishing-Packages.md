# [10] Publishing Packages

## Before You Publish: A Checklist

- [ ] Your package has a clear name (check it isn't already taken on the registry)
- [ ] `version` is set correctly (usually starting at `1.0.0` or `0.1.0` for early releases)
- [ ] A `README` explains what it does and how to use it
- [ ] A `LICENSE` file is included
- [ ] Tests pass
- [ ] You've excluded files that shouldn't be published (build artifacts, secrets, local config) — see file [16] on optimizing package size

## Publishing to npm

```bash
npm login
npm publish
```

If the name is scoped to an organization or username (e.g. `@yourname/my-package`) and it's your first publish, you may need:

```bash
npm publish --access public
```

## Publishing to PyPI

```bash
python -m build            # builds a distributable package
python -m twine upload dist/*
```

You'll need a PyPI account and, typically, an API token instead of a password.

## Publishing to crates.io

```bash
cargo login
cargo publish
```

## Semantic Versioning When Publishing Updates

Every time you publish a new version, bump the version number according to what changed (see file [5]):

```bash
npm version patch   # 1.0.0 -> 1.0.1
npm version minor   # 1.0.1 -> 1.1.0
npm version major   # 1.1.0 -> 2.0.0
```

Most registries **won't let you overwrite an already-published version** — once `1.0.0` is out, you can't republish a fixed `1.0.0`; you have to publish `1.0.1`.

## Yanking / Unpublishing

If you publish something broken, most registries offer a way to **yank** (deprecate) a version rather than delete it outright — this keeps existing users unaffected while warning new installs away from that version.

```bash
npm deprecate my-package@1.0.1 "This version has a critical bug, use 1.0.2+"
cargo yank --vers 1.0.1
```

Fully deleting a published version is usually restricted or disallowed, since other projects may already depend on it — removing it entirely could break their builds (this is exactly what happened in a well-known 2016 npm incident involving the `left-pad` package).

## Automating Publishing with CI/CD

Many teams automate publishing through a CI/CD pipeline, so a new version is published automatically when a release is tagged, rather than run manually from a developer's machine. This reduces human error and keeps a consistent, auditable publish history.

## README and Metadata Matter More Than You'd Think

Registries display your README, license, and keywords on your package's page — this is often the deciding factor for someone choosing between your package and a similar one.

## Try It Yourself

If you created a test package in file [9], try publishing it under a scoped/private name (or to a test registry) to see the full publish flow without affecting the real public registry.

## Up Next

For projects that involve multiple related packages, learn about **monorepos and workspaces**.

---
⬅ [9] [Creating Your Own Package](./%5B9%5D-Creating-Your-Own-Package.md) | ➡ [11] [Monorepos & Workspaces](./%5B11%5D-Monorepos-and-Workspaces.md)
