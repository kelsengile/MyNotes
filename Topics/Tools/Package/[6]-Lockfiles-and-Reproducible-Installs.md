# [6] Lockfiles & Reproducible Installs

## The Problem: "It Works on My Machine"

Your manifest file often specifies version *ranges* (e.g. `^2.4.1`), not exact versions. That means two different installs — on your machine today, and a teammate's machine tomorrow — could technically pull in different versions within that range, especially for transitive dependencies you never explicitly chose. This can lead to subtle bugs that only show up on some machines.

## The Solution: Lockfiles

A **lockfile** records the *exact* version of every package (direct and transitive) that was installed, down to a specific hash/checksum. When someone else installs your project using the lockfile, they get precisely the same dependency tree — not just the same allowed ranges.

| Package Manager | Lockfile |
|---|---|
| npm | `package-lock.json` |
| yarn | `yarn.lock` |
| pnpm | `pnpm-lock.yaml` |
| Poetry (Python) | `poetry.lock` |
| pip (with pip-tools) | `requirements.txt` (pinned) |
| Cargo | `Cargo.lock` |
| Bundler (Ruby) | `Gemfile.lock` |
| Composer (PHP) | `composer.lock` |

## Should You Commit the Lockfile?

**Yes — for applications.** Commit the lockfile to version control so every teammate, CI server, and production deployment installs the exact same dependency versions.

**It depends — for libraries.** Many library maintainers don't commit their lockfile, since the exact versions their *contributors* use aren't necessarily what *consumers* of the library will end up with (consumers get whatever versions satisfy the ranges in your manifest). This varies by ecosystem convention, so check what's typical for your language.

## Installing From a Lockfile

Most package managers have a strict "install exactly what the lockfile says" mode, useful in CI/CD pipelines:

```bash
npm ci                 # npm: strict, lockfile-only install
pip install -r requirements.txt   # if requirements.txt is pinned
cargo build --locked   # Cargo: fail if Cargo.lock would need to change
```

These commands fail loudly if the lockfile is out of sync with the manifest, instead of silently resolving new versions — which is exactly what you want in an automated pipeline.

## What's Actually Inside a Lockfile?

A lockfile typically records, for every package in the tree:

- The exact version installed
- Where it was downloaded from (registry URL)
- An integrity hash/checksum (to verify the downloaded file hasn't been tampered with)
- Its own resolved dependencies

You generally shouldn't hand-edit a lockfile — let the package manager regenerate it.

## Try It Yourself

1. Open your project's lockfile and search for a package you installed directly
2. Notice how much more information is there compared to your manifest file (exact version, hash, sub-dependencies)
3. Try running the "strict install" command for your package manager and see what happens if you manually edit the manifest without updating the lockfile

## Up Next

Next, you'll look at the different **scopes** packages can be installed in — locally to a project, globally on your system, or somewhere in between.

---
⬅ [5] [Versioning & Semantic Versioning](./%5B5%5D-Versioning-and-Semantic-Versioning.md) | ➡ [7] [Local vs. Global vs. Project-Level Packages](./%5B7%5D-Local-vs-Global-vs-Project-Level-Packages.md)
