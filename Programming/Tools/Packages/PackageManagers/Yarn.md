[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# Yarn

Yarn is a package manager for JavaScript, built as a faster and more reliable alternative to npm. It reads and writes the same `package.json` format, so most projects can switch between the two.

Download [https://yarnpkg.com/getting-started/install](https://yarnpkg.com/getting-started/install)

---

## What Is Yarn?

Yarn was created to address early npm pain points — inconsistent installs and slow performance. It introduced a lockfile before npm had a reliable one, and still emphasizes deterministic, cached installs.

---

## Core Commands

```bash
yarn init                    # Create a new package.json
yarn add <package>           # Install a package and add it to package.json
yarn add <package> --dev     # Install a development-only dependency
yarn remove <package>        # Remove a package
yarn install                 # Install everything listed in package.json
yarn upgrade                 # Upgrade dependencies to newer allowed versions
yarn run <script>            # Run a script defined in package.json
```

---

## yarn.lock

Like `package-lock.json` in npm, `yarn.lock` pins the exact version of every dependency installed. Commit it so every machine and CI environment installs identical versions.

---

## Yarn vs. npm

Both manage the same `package.json` dependencies and pull from the same npm registry. Yarn historically installed faster through better caching and parallel downloads; modern npm has closed much of that gap, so the choice today often comes down to team preference or existing lockfiles.

---

## Example Walkthrough

```bash
yarn init -y
yarn add express
yarn add jest --dev
yarn install
```

Creates a new project, adds Express as a dependency, adds Jest as a dev dependency, then installs everything.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)
