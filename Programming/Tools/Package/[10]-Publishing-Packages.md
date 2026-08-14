[Previous](./[9]-Creating-Your-Own-Package.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[11]-Monorepos-And-Workspaces.md)

---

# Lesson 10 - Publishing Packages

## 10.1 Before You Publish: A Checklist

- [ ] Your package has a clear name (check it isn't already taken on the registry)
- [ ] `version` is set correctly (usually starting at `1.0.0` or `0.1.0` for early releases)
- [ ] A `README` explains what it does and how to use it
- [ ] A `LICENSE` file is included
- [ ] Tests pass
- [ ] You've excluded files that shouldn't be published (build artifacts, secrets, local config) — see file [16] on optimizing package size

---

## 10.2 Publishing to npm

```bash
npm login
npm publish
```

If the name is scoped to an organization or username (e.g. `@yourname/my-package`) and it's your first publish, you may need:

```bash
npm publish --access public
```

---

## 10.3 Publishing to PyPI

```bash
python -m build            # builds a distributable package
python -m twine upload dist/*
```

You'll need a PyPI account and, typically, an API token instead of a password.

---

## 10.4 Publishing to crates.io

```bash
cargo login
cargo publish
```

---

## 10.5 Semantic Versioning When Publishing Updates

Every time you publish a new version, bump the version number according to what changed (see file [5]):

```bash
npm version patch   # 1.0.0 -> 1.0.1
npm version minor   # 1.0.1 -> 1.1.0
npm version major   # 1.1.0 -> 2.0.0
```

Most registries **won't let you overwrite an already-published version** — once `1.0.0` is out, you can't republish a fixed `1.0.0`; you have to publish `1.0.1`.

---

## 10.6 Yanking / Unpublishing

If you publish something broken, most registries offer a way to **yank** (deprecate) a version rather than delete it outright — this keeps existing users unaffected while warning new installs away from that version.

```bash
npm deprecate my-package@1.0.1 "This version has a critical bug, use 1.0.2+"
cargo yank --vers 1.0.1
```

Fully deleting a published version is usually restricted or disallowed, since other projects may already depend on it — removing it entirely could break their builds (this is exactly what happened in a well-known 2016 npm incident involving the `left-pad` package).

---

## 10.7 Automating Publishing with CI/CD

Many teams automate publishing through a CI/CD pipeline, so a new version is published automatically when a release is tagged, rather than run manually from a developer's machine. This reduces human error and keeps a consistent, auditable publish history.

---

## 10.8 README and Metadata Matter More Than You'd Think

Registries display your README, license, and keywords on your package's page — this is often the deciding factor for someone choosing between your package and a similar one.

---

[Previous](./[9]-Creating-Your-Own-Package.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[11]-Monorepos-And-Workspaces.md)
