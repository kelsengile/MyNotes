# [9] Creating Your Own Package

## When Should Code Become a Package?

Turn code into its own package when:
- You (or others) reuse it across multiple projects
- It has a clear, focused purpose
- You want to version and update it independently of any single project
- You want to share it with the community or your organization

If code is only ever used in one place and unlikely to be reused, it may not need to be its own package yet.

## Basic Structure

Most packages, regardless of language, share a similar shape:

```
my-package/
├── src/               # source code
├── tests/             # tests
├── README.md          # what it does, how to install/use it
├── LICENSE             # legal terms
├── CHANGELOG.md        # (optional) history of changes
└── <manifest file>     # package.json / pyproject.toml / Cargo.toml
```

## Example: Creating a JavaScript Package

```bash
mkdir my-package && cd my-package
npm init -y
```

This generates a `package.json`:

```json
{
  "name": "my-package",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT"
}
```

Add your code in `index.js`, and export what consumers should be able to use:

```javascript
// index.js
function greet(name) {
  return `Hello, ${name}!`;
}

module.exports = { greet };
```

## Example: Creating a Python Package

```bash
mkdir my_package && cd my_package
```

```toml
# pyproject.toml
[project]
name = "my-package"
version = "1.0.0"
description = "A simple example package"
license = "MIT"

[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"
```

```python
# my_package/__init__.py
def greet(name):
    return f"Hello, {name}!"
```

## Example: Creating a Rust Package (Crate)

```bash
cargo new my_package --lib
```

This generates a `Cargo.toml` and a `src/lib.rs` automatically.

## Writing a Good README

A README is often the first (and sometimes only) thing someone reads before deciding to use your package. A good one usually includes:

- What the package does, in one or two sentences
- Installation instructions
- A basic usage example
- Links to full documentation, if any
- License information

## Writing Tests

Package consumers rely on your code working correctly across versions. Tests catch regressions before you publish a broken update.

```bash
npm install --save-dev jest    # JavaScript
pip install pytest              # Python
# Rust testing is built into Cargo — just add #[test] functions
```

## Choosing a License

A license tells others what they're legally allowed to do with your code. Common permissive choices: MIT, Apache 2.0, BSD. Without a license file, many jurisdictions default to "all rights reserved," meaning others technically can't legally use your code even if it's public.

## Try It Yourself

Create a tiny package in your language of choice with one function, a README, and one test. Don't publish it yet — that's covered next.

## Up Next

Now that your package is built, learn how to **publish** it so others can install it.

---
⬅ [8] [Package Registries](./%5B8%5D-Package-Registries.md) | ➡ [10] [Publishing Packages](./%5B10%5D-Publishing-Packages.md)
