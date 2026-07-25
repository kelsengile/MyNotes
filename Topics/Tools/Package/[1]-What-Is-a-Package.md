# [1] What Is a Package?

## Overview

A **package** is a bundle of code and metadata that's designed to be reused across projects. Instead of writing every piece of functionality yourself, you can install a package and use the code someone else already wrote, tested, and maintains.

## What's Inside a Package?

Most packages contain some combination of:

- **Source code** — the functions, classes, or modules that do the actual work
- **A manifest file** — metadata describing the package (name, version, author, license, dependencies). Examples: `package.json` (npm), `pyproject.toml` (Python), `Cargo.toml` (Rust)
- **Documentation** — usage instructions, API references, examples
- **Tests** — code that verifies the package behaves correctly
- **A license** — legal terms for how the code can be used
- **Its own dependencies** — other packages this one relies on

## A Simple Example

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

## Packages vs. Libraries vs. Modules

These terms overlap and are often used loosely, but a common distinction:

| Term | Meaning |
|---|---|
| **Module** | A single file (or small unit) of code, usually the smallest importable piece |
| **Library** | A collection of modules that work together to provide functionality |
| **Package** | A distributable, installable unit — often a library, but with the metadata/manifest needed for a package manager to install it |

In practice, "package" and "library" are frequently used interchangeably.

## Why Not Just Copy-Paste the Code?

You could copy code from another project instead of installing it as a package, but packages give you:

- **Version tracking** — know exactly which version of the code you're using
- **Easy updates** — pull in bug fixes and security patches with one command
- **Dependency resolution** — automatically install anything that code itself depends on
- **Discoverability** — packages live in searchable registries
- **Consistency** — everyone on a team installs the exact same code

## Try It Yourself

Pick a language you're comfortable with and look at a real package's manifest file:

- JavaScript: search npm for `lodash` and view its `package.json`
- Python: search PyPI for `requests` and view its `pyproject.toml` or `setup.py`
- Rust: search crates.io for `serde` and view its `Cargo.toml`

Notice the shared structure: a name, a version, a description, and a list of dependencies.

## Up Next

In the next file, you'll learn about **package managers** — the tools that actually install, update, and remove packages for you.

---
⬅ [0] [Introduction](./%5B0%5D-Introduction.md) | ➡ [2] [Package Managers](./%5B2%5D-Package-Managers.md)
