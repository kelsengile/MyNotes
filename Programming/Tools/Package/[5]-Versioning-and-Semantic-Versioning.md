[Previous](./[4]-Dependencies-And-Dependency-Trees.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[6]-Lockfiles-And-Reproducible-Installs.md)

---

# Lesson 5 - Versioning And Semantic Versioning

## 5.1 Why Versions Matter

Every meaningful change to a package gets released as a new **version**. Version numbers let you know exactly what code you're running, decide whether it's safe to update, and communicate what kind of changes were made.

---

## 5.2 Semantic Versioning (SemVer)

The most widely used versioning scheme is **Semantic Versioning**, structured as:

```
MAJOR.MINOR.PATCH

e.g. 2.4.1
```

- **MAJOR** — incremented for breaking changes (code that used this package might need to change)
- **MINOR** — incremented for new features that don't break existing functionality
- **PATCH** — incremented for backward-compatible bug fixes

For example, going from `2.4.1` to `2.5.0` should be safe (new features, no breaking changes). Going from `2.4.1` to `3.0.0` might require changes on your end.

---

## 5.3 Version Ranges

When you specify a dependency, you rarely pin an exact version forever — you usually specify a **range** the package manager is allowed to install within. Common symbols (npm-style):

| Symbol | Meaning | Example | Allows |
|---|---|---|---|
| `^` | Compatible with (same major) | `^2.4.1` | `2.4.1` up to (not including) `3.0.0` |
| `~` | Approximately (same minor) | `~2.4.1` | `2.4.1` up to (not including) `2.5.0` |
| `>=` | At least | `>=2.4.1` | `2.4.1` or any higher version |
| (none) | Exact | `2.4.1` | Only `2.4.1` |
| `*` | Any version | `*` | Any version at all (risky) |

Other ecosystems use similar ideas with slightly different syntax — Python's `pyproject.toml` uses `>=2.4,<3.0`, and Rust's Cargo uses `^` by default even without the symbol.

---

## 5.4 Pre-Release and Build Versions

SemVer also supports labels for versions that aren't fully stable:

```
1.0.0-alpha
1.0.0-beta.2
1.0.0-rc.1        (release candidate)
```

These typically sort *before* the plain `1.0.0` release and signal "not production-ready yet."

---

## 5.5 Reading a Changelog

Before upgrading a package — especially across a major version — it's good practice to check its **changelog** or release notes. This tells you what changed, what broke, and how to migrate. Most well-maintained packages publish one (often a `CHANGELOG.md` file in their repository).

---

## 5.6 Not Every Ecosystem Follows SemVer Strictly

While SemVer is a widely adopted convention, it's not universally enforced. Some packages bump major versions more casually than the spec suggests, and some ecosystems (like Python's) have historically been looser about it. Always treat version ranges as a helpful guideline, not an absolute guarantee.

---

[Previous](./[4]-Dependencies-And-Dependency-Trees.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[6]-Lockfiles-And-Reproducible-Installs.md)
