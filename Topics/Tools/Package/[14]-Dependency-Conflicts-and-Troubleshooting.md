# [14] Dependency Conflicts & Troubleshooting

## What Is a Dependency Conflict?

A conflict happens when two (or more) packages in your dependency tree require **incompatible versions** of the same underlying package.

```
your-project
├── package-a → needs shared-lib@^1.0.0
└── package-b → needs shared-lib@^3.0.0
```

If `shared-lib`'s major version 1 and version 3 are incompatible (as SemVer implies they might be), the package manager has to decide what to do.

## How Different Ecosystems Handle This

- **npm**: usually installs multiple copies of the conflicting package, nested inside whichever package needed the different version — so `package-a` and `package-b` can each get the version they want, at the cost of some duplication
- **pip / Python**: traditionally could only install **one** version of a package per environment, so a true conflict often causes an install error, forcing you to resolve it manually (newer resolvers are stricter about catching this upfront)
- **Cargo**: allows multiple versions of a crate to coexist in the dependency graph when needed, similar to npm

## "Dependency Hell"

This term describes the frustrating experience of chasing version conflicts across many packages — updating one dependency breaks another, which requires updating a third, and so on. It's less common in ecosystems that allow multiple versions to coexist (like npm), and more common in ecosystems that require a single unified version (like traditional pip environments).

## Reading an Error Message

Conflict errors usually tell you exactly which packages want which versions. For example, a Python `pip` conflict might look like:

```
ERROR: Cannot install package-a and package-b because these package versions have conflicting dependencies.
The conflict is caused by:
    package-a 2.0.0 depends on shared-lib>=3.0.0
    package-b 1.5.0 depends on shared-lib<2.0.0
```

Read this carefully — it tells you exactly where the conflict originates, which is the first step to fixing it.

## Common Fixes

1. **Update the outdated package** — often, an older direct dependency is the one with the strict/old version constraint; updating it to a newer version may loosen that constraint
2. **Check for an alternative package** — if a dependency is unmaintained and causing conflicts, a more actively maintained alternative may resolve the issue
3. **Use a resolution/override field** — some package managers let you force a specific version for a transitive dependency:
   ```json
   // package.json (npm)
   "overrides": {
     "shared-lib": "3.2.0"
   }
   ```
4. **Isolate environments** — for Python especially, using separate virtual environments per project avoids cross-project conflicts entirely
5. **Regenerate the lockfile** — sometimes a stale lockfile is the actual source of the conflict; deleting it and reinstalling can help (with caution, since this may change other versions too)

## Debugging Strategy

1. Identify exactly which package and version are in conflict (read the error message closely)
2. Use your dependency tree command (`npm ls`, `pip show`, `cargo tree`) to see who's requiring what
3. Check if updating the direct dependency (not the conflicting one itself) resolves it
4. As a last resort, use an override/resolution to force a specific version, and test thoroughly afterward

## Try It Yourself

If you've ever hit a "conflicting dependencies" error, revisit it now with this framework — trace exactly which two packages wanted incompatible versions, and figure out which fix would have resolved it fastest.

## Up Next

Next: **license compliance** — understanding what a package's license actually permits.

---
⬅ [13] [Package Security & Supply-Chain Risks](./%5B13%5D-Package-Security-and-Supply-Chain-Risks.md) | ➡ [15] [License Compliance](./%5B15%5D-License-Compliance.md)
