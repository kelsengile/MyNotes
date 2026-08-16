[Previous](./[13]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[15]-Arrays-and-Slices.md)

*Core Syntax*

# Lesson 14 - Panic, Recover And Defer

## 14.1 defer

`defer` schedules a function call to run right before the surrounding function returns, regardless of how it returns. It's the idiomatic way to guarantee cleanup (closing files, unlocking mutexes).

```go
func readFile(path string) error {
	f, err := os.Open(path)
	if err != nil {
		return err
	}
	defer f.Close()
	// ... use f ...
	return nil
}
```

---

## 14.2 defer Order and Evaluation

Deferred calls run in LIFO (last-in, first-out) order, and their arguments are evaluated immediately even though the call happens later.

```go
for i := 0; i < 3; i++ {
	defer fmt.Println(i)
}
// prints: 2, 1, 0
```

---

## 14.3 panic

`panic` immediately stops normal execution, running any deferred calls as it unwinds the stack. It's reserved for truly unrecoverable situations — not a substitute for returning an `error`.

```go
func mustPositive(n int) int {
	if n < 0 {
		panic("n must be positive")
	}
	return n
}
```

---

## 14.4 recover

`recover` stops a panic in progress and returns the value passed to `panic`. It only has an effect when called directly inside a deferred function.

```go
func safeDivide(a, b int) (result int, err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("recovered: %v", r)
		}
	}()
	result = a / b // panics on b == 0
	return
}
```

---

## 14.5 When to Use Each

- **Return an `error`** for expected, recoverable failures (bad input, missing file). This is the Go default.
- **`panic`** for programmer errors or truly unrecoverable states (nil map write, index out of range, corrupted invariant).
- **`recover`** mainly at package boundaries — e.g. an HTTP server recovering from a panic in one request handler so it doesn't crash the whole process.

[Previous](./[13]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[15]-Arrays-and-Slices.md)
