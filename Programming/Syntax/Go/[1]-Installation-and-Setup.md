 [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[2]-Running-Go-Code.md)

*Getting Started*

# Lesson 1 - Installation And Setup

## 1.1 Installing Go

Go is distributed as a single installer for your operating system.

- **Windows/macOS**: download the installer from [go.dev/dl](https://go.dev/dl/) and run it.
- **Linux**: download the tarball and extract it to `/usr/local`:
  ```bash
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz
  ```
- **Package managers**: `brew install go` (macOS), `choco install golang` (Windows), `sudo apt install golang-go` (Debian/Ubuntu).

---

## 1.2 Setting Your PATH

Go's binary lives in a `bin` folder that needs to be on your `PATH` so you can run `go` from any terminal.

Add this to your shell profile (`.bashrc`, `.zshrc`, etc.):

```bash
export PATH=$PATH:/usr/local/go/bin
```

Restart your terminal, then verify the install:

```bash
go version
# go version go1.23.0 linux/amd64
```

---

## 1.3 Understanding GOPATH and GOROOT

- **GOROOT** — where the Go toolchain itself is installed (e.g. `/usr/local/go`). You rarely need to set this manually.
- **GOPATH** — historically where all Go code and downloaded packages lived. Modern Go (with modules, covered in Lesson 3) no longer requires you to work inside `GOPATH`, but it's still used to cache downloaded dependencies and installed binaries (`$GOPATH/pkg`, `$GOPATH/bin`).

Check your current settings:

```bash
go env GOROOT
go env GOPATH
```

---

## 1.4 Choosing an Editor

Any text editor works, but these give you Go-specific tooling (autocomplete, formatting, linting, debugging):

- **VS Code** with the official Go extension
- **GoLand** (JetBrains, Go-specific IDE)
- **Vim/Neovim** with `gopls` (the Go language server)

Whichever you choose, make sure `gofmt` runs automatically on save — Go has one standard formatting style, and idiomatic Go code always follows it.

 [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[2]-Running-Go-Code.md)
