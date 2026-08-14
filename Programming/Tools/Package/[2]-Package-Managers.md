[Previous](./[1]-What-Is-A-Package.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[3]-Installing-Updating-And-Removing-Packages.md)

---

# Lesson 2 - Package Managers

## 2.1 What Is a Package Manager?

A **package manager** is a tool that automates finding, installing, updating, configuring, and removing packages. Instead of manually downloading source code and wiring it into your project, you tell the package manager what you want, and it handles the rest — including installing anything *that* package depends on.

---

## 2.2 What a Package Manager Does

- **Installs** packages from a registry into your project or system
- **Resolves dependencies** — figures out which versions of which packages are compatible with each other
- **Updates** packages to newer versions
- **Removes** packages you no longer need
- **Tracks** what's installed via a manifest file and a lockfile
- Often provides a way to **run scripts** defined by a project (e.g. `npm run build`)

---

## 2.3 Common Package Managers by Language

| Language | Package Manager(s) | Manifest File |
|---|---|---|
| JavaScript/Node.js | npm, yarn, pnpm | `package.json` |
| Python | pip, Poetry, uv, conda | `pyproject.toml` / `requirements.txt` |
| Rust | Cargo | `Cargo.toml` |
| Ruby | Bundler (gem) | `Gemfile` |
| Java | Maven, Gradle | `pom.xml` / `build.gradle` |
| Go | Go Modules | `go.mod` |
| PHP | Composer | `composer.json` |
| C#/.NET | NuGet | `.csproj` / `packages.config` |
| Rust (system) | apt, brew, etc. | N/A (OS-level) |

---

## 2.4 Example: Installing a Package

**npm (JavaScript):**
```bash
npm install lodash
```

**pip (Python):**
```bash
pip install requests
```

**Cargo (Rust):**
```bash
cargo add serde
```

Each of these commands does roughly the same thing: downloads the package from its registry, adds it to your manifest file, and installs it (along with its dependencies) into your project.

---

## 2.5 Project-Level vs. System-Level Package Managers

Most language package managers (npm, pip, Cargo) manage packages **per project**, keeping each project's dependencies isolated. This is different from **operating system package managers** like `apt` (Debian/Ubuntu), `brew` (macOS), or `winget` (Windows), which install software system-wide.

---

## 2.6 How a Package Manager Resolves Dependencies

When you install a package, the package manager also needs to install everything *that* package depends on — and everything those depend on, and so on. This creates a **dependency tree** (covered in detail in file [4]). The package manager's job is to pick versions that satisfy every requirement in that tree without conflicts.

---

## 2.7 Choosing Between Package Managers

Some languages have more than one popular option. A few things to weigh:

- **Speed** — some managers cache and parallelize installs better than others (e.g. pnpm is generally faster than npm for large projects)
- **Determinism** — does it produce a lockfile that guarantees the same install every time?
- **Ecosystem support** — is it widely used and well-documented in your language's community?
- **Team/project convention** — consistency matters more than which one is "best"

---

[Previous](./[1]-What-Is-A-Package.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[3]-Installing-Updating-And-Removing-Packages.md)
