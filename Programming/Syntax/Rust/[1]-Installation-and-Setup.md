[Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[2]-Running-Rust-Code.md)

*Getting Started*

# Lesson 1 - Installing Rust And First-Time Setup

## 1.1 What You're Installing

Rust isn't just one program — installing it gives you three main pieces:

- **`rustc`** — the Rust compiler, which turns your source code into an executable.
- **`cargo`** — Rust's build tool and package manager (covered in depth in Lesson 3).
- **`rustup`** — the toolchain manager that installs and updates `rustc`, `cargo`, and lets you switch between Rust versions.

You'll almost never call `rustc` directly day-to-day — `cargo` calls it for you — but it's good to know it's there.

---

## 1.2 Installing rustup

On macOS and Linux, open a terminal and run:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

On Windows, download and run `rustup-init.exe` from the official Rust website, or install it via `winget install Rustlang.Rustup`.

Follow the on-screen prompts (the default install option is fine for almost everyone). Once it finishes, restart your terminal so your `PATH` picks up the new tools.

---

## 1.3 Verifying the Install

Confirm everything is set up correctly:

```bash
rustc --version
cargo --version
```

You should see version numbers printed for both, something like:

```
rustc 1.82.0 (f6e511eec 2024-10-15)
cargo 1.82.0 (8f40fc59f 2024-08-21)
```

If either command isn't found, close and reopen your terminal, or check that `~/.cargo/bin` (macOS/Linux) has been added to your `PATH`.

---

## 1.4 Keeping Rust Up to Date

Rust ships new stable releases every six weeks. Update anytime with:

```bash
rustup update
```

You can also install additional toolchains (like `nightly`, used for experimental features) alongside stable:

```bash
rustup toolchain install nightly
```

By default, `rustup` keeps you on the `stable` channel, which is what this course uses throughout.

[Table of Contents](./[0]-Introduction-to-Rust.md) | [Next](./[2]-Running-Rust-Code.md)
