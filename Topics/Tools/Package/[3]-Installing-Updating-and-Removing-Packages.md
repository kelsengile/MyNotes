# [3] Installing, Updating & Removing Packages

## Installing Packages

To add a package to your project, you use your package manager's install command. This downloads the package (and its dependencies), places the code somewhere your project can find it, and records the package in your manifest file.

**npm:**
```bash
npm install axios
```

**pip:**
```bash
pip install flask
```

**Cargo:**
```bash
cargo add tokio
```

## Installing a Specific Version

Sometimes you need an exact version, not just the latest one.

```bash
npm install axios@1.6.0
pip install flask==3.0.0
cargo add tokio@1.35.0
```

## Development-Only Dependencies

Some packages are only needed while developing (test frameworks, linters, build tools) — not when your project actually runs in production. Most package managers let you flag these separately.

```bash
npm install --save-dev jest
pip install pytest --group dev   # (varies by tool, e.g. Poetry: poetry add --group dev pytest)
```

Keeping dev dependencies separate keeps production installs smaller and faster.

## Updating Packages

**Update to the latest version allowed by your version constraints:**
```bash
npm update
pip install --upgrade flask
cargo update
```

**Check what's outdated:**
```bash
npm outdated
pip list --outdated
cargo outdated   # requires cargo-outdated
```

Updating regularly helps you get bug fixes and security patches — but it's worth reading a package's changelog before upgrading, especially across major versions (more on this in file [5]).

## Removing Packages

```bash
npm uninstall axios
pip uninstall flask
cargo remove tokio
```

This removes the package from your installed files and from your manifest file.

## Reinstalling Everything From Scratch

If you're setting up a project on a new machine (or after cloning a repo), you typically don't install packages one by one — you install everything listed in the manifest/lockfile at once:

```bash
npm install
pip install -r requirements.txt
cargo build   # Cargo installs dependencies automatically when building
```

## Where Do Installed Packages Go?

- **npm**: a `node_modules/` folder in your project directory
- **pip**: your Python environment's `site-packages/` directory (ideally inside a virtual environment)
- **Cargo**: a shared local cache (`~/.cargo/registry`), with compiled artifacts in `target/`

## Try It Yourself

1. Create a new empty project folder
2. Initialize it (`npm init -y`, or create a `pyproject.toml`, or `cargo init`)
3. Install one package
4. Update it
5. Remove it

Watch how the manifest file changes at each step.

## Up Next

Next up: understanding **dependencies and dependency trees** — what happens when the packages you install have their own dependencies.

---
⬅ [2] [Package Managers](./%5B2%5D-Package-Managers.md) | ➡ [4] [Dependencies & Dependency Trees](./%5B4%5D-Dependencies-and-Dependency-Trees.md)
