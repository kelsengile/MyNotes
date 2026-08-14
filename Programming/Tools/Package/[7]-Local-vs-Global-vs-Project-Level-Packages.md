[Previous](./[6]-Lockfiles-And-Reproducible-Installs.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[8]-Package-Registries.md)

---

# Lesson 7 - Local Vs. Global Vs. Project-Level Packages

## 7.1 Project-Level (Local) Installs

By default, most package managers install packages **into your current project only**, isolated from every other project on your machine.

```bash
npm install lodash        # installs into ./node_modules
cargo add serde            # installs into this project's dependency graph
```

This is almost always what you want for project dependencies: Project A can use `lodash@3.0.0` while Project B uses `lodash@4.0.0`, with zero conflict, because each has its own isolated copy.

---

## 7.2 Global Installs

Some tools are meant to be used from the command line across *any* project — a formatter, a scaffolding tool, a CLI utility. These are often installed **globally**, making the command available system-wide.

```bash
npm install -g typescript
pip install --user httpie
cargo install ripgrep
```

Global installs have downsides:
- No isolation — every project on your machine shares the same global version
- Harder to reproduce for teammates (a global tool isn't recorded in your project's manifest)
- Can silently break if you update the global version and a project needed an older one

**General guidance:** prefer project-level installs whenever a package is something your *code* imports and depends on. Reserve global installs for standalone CLI tools you personally run, not things your project logic relies on.

---

## 7.3 Running a Package Without Installing It Globally

Modern tooling often lets you run a package's CLI once, without a permanent global install:

```bash
npx create-react-app my-app     # npm
pipx run cowsay "hello"          # Python
cargo install --list             # cargo has no direct equivalent, but similar tools exist
```

This avoids polluting your global environment while still letting you use one-off tools.

---

## 7.4 Virtual Environments (Python's Special Case)

Python doesn't isolate dependencies per-project by default the way npm or Cargo do — installing with plain `pip` affects your whole Python installation. That's why Python projects typically use a **virtual environment**:

```bash
python -m venv .venv
source .venv/bin/activate     # macOS/Linux
.venv\Scripts\activate         # Windows

pip install flask              # installs only into this virtual environment
```

Tools like Poetry and `uv` manage virtual environments for you automatically.

---

## 7.5 Project-Level "Local Bin" Tools

Even dev tools (linters, test runners, build scripts) are usually best installed at the project level, not globally — this ensures every contributor and your CI pipeline use the exact same tool version, tracked in your lockfile.

---

[Previous](./[6]-Lockfiles-And-Reproducible-Installs.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[8]-Package-Registries.md)
