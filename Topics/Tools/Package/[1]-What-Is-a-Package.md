[Previous](./[0]-Introduction.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[2]-Package-Managers.md)

---

# Lesson 1 - What Is A Package?

## 1.1 Overview

A **package** is a bundle of code and metadata that's designed to be reused across projects. Instead of writing every piece of functionality yourself, you can install a package and use the code someone else already wrote, tested, and maintains.

---

## 1.2 What's Inside a Package?

Most packages contain some combination of:

- **Source code** — the functions, classes, or modules that do the actual work
- **A manifest file** — metadata describing the package (name, version, author, license, dependencies). Examples: `package.json` (npm), `pyproject.toml` (Python), `Cargo.toml` (Rust)
- **Documentation** — usage instructions, API references, examples
- **Tests** — code that verifies the package behaves correctly
- **A license** — legal terms for how the code can be used
- **Its own dependencies** — other packages this one relies on

---

## 1.3 A Simple Example

Here's a minimal `package.json` for a JavaScript package:

```json
{
  "name": "left-pad",
  "version": "1.3.0",
  "description": "Pad a string on the left",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {}
}
```

And a minimal Python `pyproject.toml`:

```toml
[project]
name = "left-pad"
version = "1.3.0"
description = "Pad a string on the left"
license = "MIT"
```

Both describe the same idea: a small, focused piece of reusable code with a name, a version, and metadata that tools can read.

---

## 1.4 Packages vs. Libraries vs. Modules

These terms overlap and are often used loosely, but a common distinction:

| Term | Meaning |
|---|---|
| **Module** | A single file (or small unit) of code, usually the smallest importable piece |
| **Library** | A collection of modules that work together to provide functionality |
| **Package** | A distributable, installable unit — often a library, but with the metadata/manifest needed for a package manager to install it |

In practice, "package" and "library" are frequently used interchangeably.

---

## 1.5 Why Not Just Copy-Paste the Code?

You could copy code from another project instead of installing it as a package, but packages give you:

- **Version tracking** — know exactly which version of the code you're using
- **Easy updates** — pull in bug fixes and security patches with one command
- **Dependency resolution** — automatically install anything that code itself depends on
- **Discoverability** — packages live in searchable registries
- **Consistency** — everyone on a team installs the exact same code

---

[Previous](./[0]-Introduction.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[2]-Package-Managers.md)
