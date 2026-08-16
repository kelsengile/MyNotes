[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[3]-Cargo-and-Dependency-Management.md)

*Getting Started*

# Lesson 2 - Running Code: cargo run, cargo build And The Rust Toolchain

## 2.1 Creating a New Project

Instead of creating files by hand, let Cargo scaffold a project for you:

```bash
cargo new hello_rust
cd hello_rust
```

This creates a folder with a `Cargo.toml` file (project metadata and dependencies) and a `src/main.rs` file containing:

```rust
fn main() {
    println!("Hello, world!");
}
```

`fn main()` is the entry point every executable Rust program starts from.

---

## 2.2 Building With cargo build

`cargo build` compiles your project without running it:

```bash
cargo build
```

This produces a binary at `target/debug/hello_rust` (or `.exe` on Windows). Debug builds compile fast but run slower, which is ideal while developing.

For an optimized production build, add `--release`:

```bash
cargo build --release
```

This is slower to compile but produces a much faster binary, placed in `target/release/`.

---

## 2.3 Running With cargo run

Most of the time you want to compile *and* run in one step:

```bash
cargo run
```

Cargo recompiles only if your source files changed since the last build, then runs the resulting binary and streams its output to your terminal.

---

## 2.4 Checking Code Without Building

`cargo check` verifies your code compiles — catching type errors and typos — without producing a binary. It's much faster than `cargo build`, so it's useful while actively writing code:

```bash
cargo check
```

A typical workflow is to run `cargo check` constantly while editing, and `cargo run` when you're ready to see the program actually execute.

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[3]-Cargo-and-Dependency-Management.md)
