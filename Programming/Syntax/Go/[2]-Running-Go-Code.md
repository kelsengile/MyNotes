[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[3]-Go-Modules-and-Dependency-Management.md)

*Getting Started*

# Lesson 2 - Running Go Code

## 2.1 Your First Program

Create a file named `main.go`:

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, Go!")
}
```

Every executable Go program needs a `package main` and a `func main()` — that's the entry point the toolchain looks for.

---

## 2.2 go run

`go run` compiles and executes your program in one step, without leaving a binary behind. It's the fastest way to try things out while developing.

```bash
go run main.go
# Hello, Go!
```

---

## 2.3 go build

`go build` compiles your program into a standalone binary you can distribute and run without Go installed.

```bash
go build -o hello main.go
./hello
# Hello, Go!
```

Go binaries are statically linked by default, so `./hello` runs on its own with no external dependencies.

---

## 2.4 go install

`go install` builds a binary and places it in `$GOPATH/bin` (or `$GOBIN`), making it available as a command from anywhere on your `PATH`. This is how you install Go-based CLI tools.

```bash
go install github.com/some/tool@latest
```

---

## 2.5 Other Useful Toolchain Commands

- `go fmt` — reformats your code to the standard Go style.
- `go vet` — analyzes code for common mistakes (like `fmt.Printf` calls with mismatched arguments).
- `go doc` — prints documentation for a package or symbol.
- `go clean` — removes build artifacts.

```bash
go fmt ./...
go vet ./...
```

The `./...` pattern means "this directory and every subdirectory" — you'll see it constantly in Go tooling.

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[3]-Go-Modules-and-Dependency-Management.md)
