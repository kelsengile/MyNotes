[Previous](./[2]-Running-Go-Code.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[4]-Workspace-Layout-and-Project-Structure.md)

*Getting Started*

# Lesson 3 - Go Modules And Dependency Management

## 3.1 What Is a Module?

A **module** is a collection of Go packages versioned and distributed together, defined by a `go.mod` file at its root. Modules are how Go manages dependencies since Go 1.11, replacing the old `GOPATH`-based workflow.

Create a new module:

```bash
go mod init github.com/yourname/yourproject
```

This generates a `go.mod` file:

```
module github.com/yourname/yourproject

go 1.23
```

---

## 3.2 Adding Dependencies

Simply import a package and run `go mod tidy` — Go downloads the dependency, adds it to `go.mod`, and records exact versions in `go.sum`.

```go
import "github.com/google/uuid"
```

```bash
go mod tidy
```

- **go.mod** — lists your module's direct dependencies and their minimum versions.
- **go.sum** — cryptographic checksums of every dependency, ensuring reproducible, tamper-proof builds.

---

## 3.3 Common Module Commands

```bash
go get github.com/google/uuid@v1.6.0   # add/upgrade a specific version
go get -u ./...                         # upgrade all dependencies
go mod tidy                             # add missing, remove unused
go mod vendor                           # copy dependencies into a local vendor/ folder
go list -m all                          # list the full dependency graph
```

---

## 3.4 Semantic Versioning

Go dependencies follow [semver](https://semver.org/): `vMAJOR.MINOR.PATCH`. Modules at major version 2 or higher must include the version in their import path (e.g. `github.com/user/repo/v2`), which lets multiple incompatible major versions coexist in the same build.

[Previous](./[2]-Running-Go-Code.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[4]-Workspace-Layout-and-Project-Structure.md)
