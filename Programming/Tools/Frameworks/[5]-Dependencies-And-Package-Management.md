[Previous](./[4]-Project-Structure-And-Conventions.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[6]-Architecture-Patterns.md)

*Application Structure*

# Lesson 5 - Dependencies And Package Management

No modern project is written entirely from scratch. A framework project pulls in dozens, sometimes thousands, of pieces of code written by other people. Managing those pieces responsibly is a skill in its own right. This lesson explains what dependencies are, how package managers work, how versioning and lock files keep builds reproducible, and how to keep your dependencies safe.

## 5.1 What Is a Dependency

A **dependency** is a piece of external code that your project needs in order to work. The framework itself is a dependency, and so are the date-formatting library, the database driver, and the testing tool you add later.

Instead of copying other people's code into your project, you *declare* that you depend on it, and a package manager downloads it for you. This makes it easy to update, easy to remove, and clear to anyone reading your project exactly what it relies on.

### Direct and transitive dependencies

- **Direct dependencies** are the packages you chose to add yourself.
- **Transitive dependencies** are the packages that *your dependencies* need in turn.

```text
your-app
├── framework            (direct)
│   ├── router-helper    (transitive)
│   └── http-parser      (transitive)
└── date-library         (direct)
    └── locale-data      (transitive)
```

You may declare only a handful of direct dependencies and still end up with hundreds of packages installed. That is normal, and it is why the security practices at the end of this lesson matter.

### Runtime and development dependencies

Not every dependency is needed in the finished application.

| Kind | Needed when? | Examples |
|---|---|---|
| **Runtime (production)** | When the app runs for real users | The framework, a database driver |
| **Development** | Only while building and testing | Test runners, linters, bundlers |

In `package.json` they are separated into `dependencies` and `devDependencies`. Other ecosystems have similar mechanisms (for example, separate requirements files in Python or a `require-dev` section in Composer). Keeping them separate means your production deployment installs only what it actually needs.

### Why dependencies can be a problem

Dependencies save huge amounts of work, but each one is also a risk:

- It may contain **bugs or security flaws**.
- It may be **abandoned** by its maintainers.
- It may release a **breaking change** that stops your code from working.
- Two of your dependencies may need **incompatible versions** of a third one (sometimes called "dependency hell").

Good dependency management is about enjoying the benefits while limiting those risks.

---

## 5.2 Package Managers (npm, pip, Composer, and more)

A **package manager** is a tool that finds, downloads, installs, updates, and removes dependencies, and records them in your project's manifest file. Packages come from a **registry**, an online catalog of published packages.

| Ecosystem | Package manager | Manifest | Registry |
|---|---|---|---|
| JavaScript / TypeScript | npm, yarn, pnpm | `package.json` | npm registry |
| Python | pip (also Poetry, uv) | `requirements.txt`, `pyproject.toml` | PyPI |
| Java | Maven, Gradle | `pom.xml`, `build.gradle` | Maven Central |
| PHP | Composer | `composer.json` | Packagist |
| C# | NuGet (via the `dotnet` CLI) | `.csproj` | nuget.org |
| Ruby | Bundler | `Gemfile` | RubyGems |
| Dart / Flutter | pub | `pubspec.yaml` | pub.dev |

### Common commands

Each tool has its own commands, but they all do the same few jobs:

| Job | npm | pip | Composer | Flutter / Dart |
|---|---|---|---|---|
| Install everything listed in the manifest | `npm install` | `pip install -r requirements.txt` | `composer install` | `flutter pub get` |
| Add a package | `npm install <name>` | `pip install <name>` | `composer require <name>` | `flutter pub add <name>` |
| Remove a package | `npm uninstall <name>` | `pip uninstall <name>` | `composer remove <name>` | `flutter pub remove <name>` |
| Update packages | `npm update` | `pip install --upgrade <name>` | `composer update` | `flutter pub upgrade` |

For .NET, the equivalent of adding a package is `dotnet add package <name>`. Maven and Gradle projects declare dependencies directly in `pom.xml` or `build.gradle`, and the build tool downloads them.

### Local vs global installs

- A **local** install puts packages inside the project, so each project has its own versions. This is the normal choice for application dependencies.
- A **global** install makes a tool available everywhere on your machine. It is useful for command-line tools but risky for project libraries, because different projects would have to share one version.

### Isolating Python projects with virtual environments

Python's pip installs packages into one shared location by default, which can cause projects to interfere with each other. A **virtual environment** gives each project its own isolated set of packages:

```bash
python -m venv .venv            # create the environment in a folder named .venv
source .venv/bin/activate       # activate it (on Windows: .venv\Scripts\activate)
pip install flask               # installs into .venv only
pip freeze > requirements.txt   # record the installed versions
```

Other ecosystems solve the same problem differently: npm installs into a project's own `node_modules/` folder, and Composer installs into `vendor/`.

---

## 5.3 Versioning and Lock Files

### Semantic versioning

Most packages use **semantic versioning (SemVer)**, a three-number scheme: `MAJOR.MINOR.PATCH`, for example `4.17.21`.

| Part | Increases when... | Safe to update? |
|---|---|---|
| **MAJOR** (`4`.17.21) | There are **breaking changes** | Maybe not: read the release notes |
| **MINOR** (4.`17`.21) | New features are added in a backward-compatible way | Usually yes |
| **PATCH** (4.17.`21`) | Bugs are fixed in a backward-compatible way | Usually yes |

SemVer is a *promise* made by maintainers, not a guarantee enforced by tools, so occasional surprises do happen.

### Version ranges

Manifests rarely demand one exact version. They describe a **range** of acceptable versions.

| Notation (npm) | Meaning | Accepts |
|---|---|---|
| `1.2.3` | Exactly this version | only 1.2.3 |
| `^1.2.3` | Compatible changes (caret) | 1.2.3 up to, but not including, 2.0.0 |
| `~1.2.3` | Patch-level changes (tilde) | 1.2.3 up to, but not including, 1.3.0 |

Other ecosystems have their own notations. Python's pip uses operators such as `==1.2.3` (exact), `>=1.2.3` (minimum), and `~=1.2.3` (compatible release, meaning `>=1.2.3` and `<1.3.0`).

### The problem ranges create

If your manifest says `^1.2.3`, then today `npm install` might fetch 1.2.3, but next month it might fetch 1.9.0, and a teammate installing on a different day could get a different version than you. That makes bugs hard to reproduce: "it works on my machine."

### Lock files

A **lock file** solves this by recording the *exact* version of **every** package that was installed, direct and transitive. When someone installs from the lock file, they get precisely the same versions.

| Ecosystem | Lock file |
|---|---|
| npm | `package-lock.json` |
| yarn | `yarn.lock` |
| pnpm | `pnpm-lock.yaml` |
| Composer | `composer.lock` |
| Bundler | `Gemfile.lock` |
| Poetry / uv | `poetry.lock` / `uv.lock` |
| pub (Dart / Flutter) | `pubspec.lock` |

Guidelines for lock files:

- **Commit the lock file** to version control for applications, so every developer and your deployment get identical dependencies.
- **Do not edit it by hand.** The package manager maintains it.
- Use the "install from lock file" command in automated builds (for example, `npm ci`), which fails if the manifest and lock file disagree, instead of silently changing versions.

The manifest says *what you are willing to accept*. The lock file says *exactly what you got*.

---

## 5.4 Keeping Dependencies Safe and Updated

Because your application runs other people's code, its security depends partly on theirs. Attackers know this and target popular packages. Healthy dependency habits are part of everyday development.

### Known vulnerabilities

Security researchers publish known flaws in public databases. Most ecosystems have a built-in or add-on tool that checks your dependencies against them:

| Ecosystem | Example command |
|---|---|
| npm | `npm audit` |
| Python | `pip-audit` (a separate tool) |
| Composer | `composer audit` |
| .NET | `dotnet list package --vulnerable` |
| Java | OWASP Dependency-Check (a separate tool) |

Hosting platforms such as GitHub can also scan your repository automatically and warn you about vulnerable packages.

### Supply-chain risks

A **supply-chain attack** targets the code you depend on rather than your own code. Common forms include:

- **Typosquatting**: a malicious package published under a name that looks almost identical to a popular one (for example, one letter different), hoping someone mistypes an install command.
- **Compromised maintainers**: an attacker takes over a legitimate package and publishes a harmful update.
- **Abandoned packages**: nobody is left to fix security issues.

### Good habits

1. **Check the name carefully** before installing anything new.
2. **Evaluate a package before you adopt it.** Look at how recently it was updated, how many people use it, whether issues get answered, and what license it uses.
3. **Prefer fewer dependencies.** Every package you add is something you must keep safe and updated. Don't add a package for something trivial.
4. **Commit and respect your lock file** so installs are reproducible.
5. **Update regularly, in small steps.** Frequent small updates are far easier to fix than one giant jump after years. Read release notes for major versions.
6. **Run your tests after updating** to catch breaking changes early (Lesson 16).
7. **Automate the boring part.** Tools such as Dependabot and Renovate open pull requests when updates are available, so you can review and test them one at a time.
8. **Remove dependencies you no longer use.**
9. **Keep secrets out of packages and repositories**, and use only trusted registries.

---

[Previous](./[4]-Project-Structure-And-Conventions.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[6]-Architecture-Patterns.md)
