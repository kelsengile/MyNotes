[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# Cargo

Cargo is Rust's official package manager and build tool. It downloads dependencies ("crates"), compiles your project, and manages versioning through a lockfile — all from one command.

Download [https://doc.rust-lang.org/cargo/](https://doc.rust-lang.org/cargo/)

---

## What Is Cargo?

Every Rust project has a `Cargo.toml` file listing its dependencies and a `Cargo.lock` file pinning their exact versions. Cargo reads both to fetch the right code from [crates.io](https://crates.io) and build your project, so you rarely touch dependencies by hand.

---

## Core Commands

```bash
cargo new my_project         # Create a new Rust project
cargo add <crate>            # Add a dependency to Cargo.toml
cargo build                  # Download dependencies and compile the project
cargo build --release        # Compile with optimizations for production
cargo run                    # Build and run the project in one step
cargo remove <crate>         # Remove a dependency
cargo update                 # Update dependencies to their latest compatible versions
cargo test                   # Run the project's tests
```

---

## Crates and the Registry

Packages in the Rust ecosystem are called **crates**. By default, `cargo add` and `cargo build` pull crates from [crates.io](https://crates.io), the official public registry — no separate configuration needed.

```toml
# Cargo.toml
[dependencies]
serde = "1.0"
```

---

## Lockfiles

`Cargo.lock` records the exact version of every dependency (and their dependencies) used in a build. Commit it for applications so every machine builds with identical versions; libraries typically leave it out of version control.

---

## Example Walkthrough

```bash
cargo new hello_cargo
cd hello_cargo
cargo add rand
cargo build
cargo run
```

Creates a new project, adds the `rand` crate, compiles it, and runs the resulting binary.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)
