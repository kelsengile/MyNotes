[Previous](./[28]-Sync-Package.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[30]-Concurrency-Patterns.md)

*Concurrency*

# Lesson 29 - Context Package

## 29.1 What context Is For

`context.Context` carries cancellation signals, deadlines, and request-scoped values across API boundaries and goroutines — essential for cleanly stopping work that's no longer needed (a client disconnects, a timeout fires).

```go
func doWork(ctx context.Context) error {
	select {
	case <-time.After(5 * time.Second):
		return nil
	case <-ctx.Done():
		return ctx.Err()
	}
}
```

---

## 29.2 Creating Contexts

```go
ctx := context.Background()      // root context, typically in main() or a test
ctx := context.TODO()             // placeholder when unsure which context to use yet
```

---

## 29.3 Cancellation

```go
ctx, cancel := context.WithCancel(context.Background())
defer cancel() // always call cancel to release resources

go doWork(ctx)
cancel() // signals doWork to stop via ctx.Done()
```

---

## 29.4 Timeouts and Deadlines

```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

ctx2, cancel2 := context.WithDeadline(context.Background(), time.Now().Add(1*time.Hour))
defer cancel2()
```

Once the timeout or deadline passes, `ctx.Done()` closes and `ctx.Err()` returns `context.DeadlineExceeded`.

---

## 29.5 Passing Values

`context.WithValue` attaches request-scoped data (like a request ID) — use sparingly, only for data that's truly cross-cutting, never for passing regular function parameters.

```go
ctx := context.WithValue(context.Background(), requestIDKey, "abc-123")
id := ctx.Value(requestIDKey)
```

---

## 29.6 Convention: ctx as the First Parameter

Idiomatic Go functions that do I/O or long-running work accept `ctx context.Context` as their first parameter, by convention, so callers can always cancel or time out the call.

```go
func FetchUser(ctx context.Context, id string) (*User, error)
```

[Previous](./[28]-Sync-Package.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[30]-Concurrency-Patterns.md)
