[Previous](./[3]-Go-Modules-and-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - Workspace Layout And Project Structure

## 4.1 A Minimal Project

A single-file program only needs `go.mod` and `main.go`:

```
myapp/
├── go.mod
└── main.go
```

---

## 4.2 The Standard Layout for Larger Projects

As projects grow, the community has converged on a loose convention (documented informally in `golang-standards/project-layout`):

```
myapp/
├── go.mod
├── go.sum
├── main.go              # or cmd/myapp/main.go for multiple binaries
├── internal/             # private packages, not importable by other modules
│   ├── handler/
│   └── service/
├── pkg/                  # packages safe for external use
├── api/                  # API definitions (OpenAPI, protobuf)
├── configs/               # configuration files
└── scripts/               # build/deploy scripts
```

---

## 4.3 The internal Package Convention

Any package under a directory named `internal/` can only be imported by code inside the same module tree rooted at that `internal`'s parent. This is enforced by the compiler, not just convention — it's Go's built-in mechanism for marking code as private to your project.

```
myapp/internal/auth/  → importable only within myapp/
```

---

## 4.4 Multiple Binaries with cmd/

If a module produces more than one executable, put each `main` package in its own subfolder under `cmd/`:

```
myapp/
├── cmd/
│   ├── server/main.go
│   └── worker/main.go
└── internal/...
```

Build a specific binary with:

```bash
go build ./cmd/server
```

[Previous](./[3]-Go-Modules-and-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[5]-Variables-and-Data-Types.md)
