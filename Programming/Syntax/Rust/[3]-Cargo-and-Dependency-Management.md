[Previous](./[2]-Running-Rust-Code.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[4]-Project-Structure-and-Workspaces.md)

*Getting Started*

# Lesson 3 - Cargo And Dependency Management

## 3.1 Anatomy of Cargo.toml

`Cargo.toml` is the manifest file describing your project. A fresh project looks like:

```toml
[package]
name = "hello_rust"
version = "0.1.0"
edition = "2021"

[dependencies]
```

- `[package]` holds metadata: name, version, and the language *edition* (a set of Rust conventions — `2021` is the current standard).
- `[dependencies]` lists external crates (Rust's term for packages/libraries) your project needs.

---

## 3.2 Adding Dependencies

External crates live on [crates.io](https://crates.io), Rust's package registry. Add one with:

```bash
cargo add rand
```

This edits `Cargo.toml` automatically:

```toml
[dependencies]
rand = "0.8"
```

Then use it in code:

```rust
use rand::Rng;

fn main() {
    let n: u32 = rand::thread_rng().gen_range(1..=100);
    println!("Random number: {n}");
}
```

Running `cargo run` will download, compile, and link the crate automatically the first time.

---

## 3.3 Semantic Versioning

Crate versions follow **semver**: `MAJOR.MINOR.PATCH`. A dependency listed as `"0.8"` means "any compatible 0.8.x release." Cargo won't silently jump to a breaking `0.9` or `1.0` version without your permission.

---

## 3.4 Cargo.lock

The first time you build, Cargo generates `Cargo.lock`, which pins the *exact* resolved version of every dependency (including dependencies of dependencies). This guarantees that everyone building your project — teammates, CI servers, production — gets identical versions.

- For applications (binaries), commit `Cargo.lock` to version control.
- For libraries, it's conventional to leave it out, since the consuming application controls the final versions.

To update dependencies within their allowed semver range:

```bash
cargo update
```

[Previous](./[2]-Running-Rust-Code.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[4]-Project-Structure-and-Workspaces.md)
