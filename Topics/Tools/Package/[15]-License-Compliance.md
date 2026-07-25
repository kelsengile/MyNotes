# [15] License Compliance

## Why Licenses Matter

Every package you install was written by someone else, and its **license** determines what you're legally allowed to do with it — use it commercially, modify it, redistribute it, or combine it with your own code. Ignoring licensing isn't just a legal formality; using a package in a way its license doesn't allow can create real legal risk for you or your company.

## Common Open-Source Licenses

| License | Summary |
|---|---|
| **MIT** | Very permissive — use, modify, distribute, even in proprietary software, with attribution |
| **Apache 2.0** | Similar to MIT, plus explicit patent grant protections |
| **BSD (2-clause/3-clause)** | Permissive, similar in spirit to MIT |
| **GPL (v2/v3)** | "Copyleft" — if you distribute software that includes GPL code, your code may need to be open-sourced under GPL too |
| **LGPL** | A more limited copyleft, generally allows linking from proprietary software |
| **MPL 2.0** | File-level copyleft — modified MPL files must stay open, but you can combine with proprietary code elsewhere |
| **Unlicense / Public Domain** | No restrictions at all |
| **Proprietary / All Rights Reserved** | No usage rights granted beyond what's explicitly stated (or none, if unstated) |

## Permissive vs. Copyleft

The most important distinction to understand:

- **Permissive licenses** (MIT, Apache, BSD) generally let you use the code in almost any project, including closed-source/commercial ones, with minimal obligations (usually just keeping the original license/attribution notice).
- **Copyleft licenses** (GPL family) come with obligations that can extend to *your* code — depending on how you use the package, you may be required to release your own source code under a compatible license too.

For a company shipping proprietary software, accidentally depending on a strong copyleft package can be a serious legal issue.

## Checking a Package's License

Most manifest files declare a license directly:

```json
// package.json
{ "license": "MIT" }
```

```toml
# pyproject.toml
[project]
license = "MIT"
```

You can also check a project's `LICENSE` file in its repository, or its listing on the registry (npm, PyPI, and crates.io all display license info on package pages).

## Auditing Licenses Across Your Dependency Tree

Because a single install can pull in hundreds of transitive dependencies, manually checking every license isn't practical — tools exist to automate it:

```bash
npx license-checker              # JavaScript
pip-licenses                       # Python (third-party tool)
cargo license                       # Rust (third-party tool)
```

These can flag anything with a copyleft or unrecognized license so you can review it before shipping.

## Some Packages Have No License at All

If a package (or its own transitive dependencies) has no license file or declaration, that's a legal gray area — in most jurisdictions, no license means no explicit permission is granted, even if the code is publicly visible on a registry. This is worth flagging rather than assuming it's safe to use.

## Try It Yourself

Run a license-checking tool (or manually check a few manifest files) on a project you work on, and see whether any dependencies use a copyleft license you weren't aware of.

## Up Next

Finally, learn how to **optimize install size and build performance** as your dependency tree grows.

---
⬅ [14] [Dependency Conflicts & Troubleshooting](./%5B14%5D-Dependency-Conflicts-and-Troubleshooting.md) | ➡ [16] [Optimizing Install Size & Build Performance](./%5B16%5D-Optimizing-Install-Size-and-Build-Performance.md)
