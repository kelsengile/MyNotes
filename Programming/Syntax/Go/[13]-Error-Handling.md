[Previous](./[12]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[14]-Panic-Recover-and-Defer.md)

*Core Syntax*

# Lesson 13 - Error Handling

## 13.1 The error Interface

Go has no exceptions for ordinary failures — errors are just values that satisfy the built-in `error` interface:

```go
type error interface {
	Error() string
}
```

Functions that can fail return an `error` as their last result, and callers check it explicitly:

```go
f, err := os.Open("file.txt")
if err != nil {
	log.Fatal(err)
}
```

---

## 13.2 Creating Errors

```go
errors.New("something went wrong")
fmt.Errorf("failed to process user %d", userID)
```

---

## 13.3 Wrapping Errors

`%w` in `fmt.Errorf` wraps an underlying error, preserving it for later inspection while adding context.

```go
if err != nil {
	return fmt.Errorf("loading config: %w", err)
}
```

---

## 13.4 errors.Is and errors.As

- `errors.Is` checks whether an error (or anything it wraps) matches a specific sentinel error.
- `errors.As` checks whether an error (or anything it wraps) matches a specific error *type*, and unwraps it into a variable.

```go
var ErrNotFound = errors.New("not found")

if errors.Is(err, ErrNotFound) {
	// handle missing record
}

var pathErr *os.PathError
if errors.As(err, &pathErr) {
	fmt.Println(pathErr.Path)
}
```

---

## 13.5 Custom Error Types

Any type with an `Error() string` method satisfies the `error` interface.

```go
type ValidationError struct {
	Field string
}

func (e *ValidationError) Error() string {
	return fmt.Sprintf("invalid field: %s", e.Field)
}
```

[Previous](./[12]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[14]-Panic-Recover-and-Defer.md)
