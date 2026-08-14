[Previous](./[15]-License-Compliance.md) | [Table of Contents](./[0]-Introduction.md)

---

# Lesson 16 - Optimizing Install Size And Build Performance

## 16.1 Why This Matters

As a project grows, its dependency tree can balloon into hundreds or thousands of packages. This slows down installs, bloats build artifacts, increases attack surface (file [13]), and can noticeably hurt CI pipeline times. A little attention here pays off across the whole life of a project.

---

## 16.2 Auditing Your Dependency Size

```bash
npm ls --all | wc -l                  # rough count of installed packages
npx cost-of-modules                    # JavaScript: shows largest dependencies by size
du -sh node_modules                     # total install size

pip list | wc -l                        # rough count of installed Python packages
cargo tree | wc -l                       # rough count in a Rust project
```

---

## 16.3 Removing Unused Dependencies

Over time, projects accumulate dependencies that are no longer actually used in the code. Tools can detect these automatically:

```bash
npx depcheck              # JavaScript: finds unused dependencies
pip-check                  # Python: various tools exist for this
cargo-udeps                 # Rust: finds unused dependencies (nightly toolchain)
```

Removing unused packages shrinks your install, your lockfile, and your audit surface all at once.

---

## 16.4 Choosing Lighter Alternatives

Sometimes a smaller, more focused package can replace a large one that's only being used for a fraction of its features. Before reaching for a big library, it's worth asking whether the functionality you need is small enough to write yourself, or covered by a lighter-weight alternative.

---

## 16.5 Deduplication

Because of how dependency trees resolve (file [4]), you can end up with multiple copies of the same package at different versions. Some tools can flatten/deduplicate compatible versions to reduce redundancy:

```bash
npm dedupe
```

---

## 16.6 Excluding Files From Published Packages

When you publish your own package, only files consumers actually need should be included — not tests, source maps, CI config, or documentation drafts. Most ecosystems support an ignore/allowlist file:

```
# .npmignore, or a "files" field in package.json
tests/
*.test.js
.github/
```

```toml
# Cargo.toml
[package]
exclude = ["tests/*", "examples/*"]
```

Smaller published packages install faster for everyone who depends on you.

---

## 16.7 Caching to Speed Up Installs

Package managers cache downloaded packages locally so repeat installs (on your machine, or in CI) don't need to re-download from the registry every time:

```bash
npm config get cache
cargo --version   # cargo caches to ~/.cargo/registry automatically
```

In CI pipelines, explicitly caching this directory between runs (a common feature in GitHub Actions, GitLab CI, etc.) can cut install time dramatically.

---

## 16.8 Lazy-Loading and Code Splitting

For applications (especially front-end ones), not every dependency needs to be loaded immediately — bundlers can split code so large or rarely used dependencies are only downloaded by the end user when actually needed, rather than bloating the initial load.

---

## Series Complete 🎉

You've now covered the full lifecycle of working with packages — from what they are, to installing and managing them, creating and publishing your own, and keeping them secure, license-compliant, and performant. Head back to the [Introduction](./[0]-Introduction.md) to review the full Table of Contents.

---

[Previous](./[15]-License-Compliance.md) | [Table of Contents](./[0]-Introduction.md)
