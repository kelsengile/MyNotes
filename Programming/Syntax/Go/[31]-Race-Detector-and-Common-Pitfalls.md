[Previous](./[30]-Concurrency-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[32]-Working-with-Files.md)

*Concurrency*

# Lesson 31 - The Race Detector And Common Pitfalls

## 31.1 What Is a Data Race?

A data race happens when two goroutines access the same variable concurrently, and at least one of them writes, without synchronization. The result is undefined behavior — not just "sometimes wrong," but genuinely unpredictable.

```go
var counter int

func increment() {
	counter++ // read-modify-write: not atomic, not safe concurrently
}

for i := 0; i < 1000; i++ {
	go increment()
}
```

---

## 31.2 The -race Flag

Go ships with a built-in race detector. Run it during development and in CI — it instruments memory accesses and reports races it actually observes at runtime.

```bash
go run -race main.go
go test -race ./...
```

The detector only catches races that occur during the run, so it complements but doesn't replace careful code review.

---

## 31.3 Fixing Races

The two standard fixes: protect shared state with a `sync.Mutex`, or avoid sharing it at all by communicating over a channel.

```go
var mu sync.Mutex
var counter int

func increment() {
	mu.Lock()
	defer mu.Unlock()
	counter++
}
```

For simple counters, `sync/atomic` is a lighter-weight alternative to a full mutex:

```go
var counter atomic.Int64
counter.Add(1)
```

---

## 31.4 Common Pitfall: Goroutine Leaks

A goroutine blocked forever on a channel that will never receive/send is a leak — it never gets garbage collected. Always give goroutines a way out, typically via `context` cancellation or a dedicated quit channel.

```go
func leaky() {
	ch := make(chan int)
	go func() {
		ch <- 1 // blocks forever if nobody ever reads
	}()
	// ch is never read — goroutine leaks
}
```

---

## 31.5 Common Pitfall: Deadlocks

A deadlock occurs when goroutines wait on each other in a cycle, so none can proceed. Go detects the trivial case (all goroutines asleep) and crashes with `fatal error: all goroutines are asleep - deadlock!`.

```go
func deadlock() {
	ch := make(chan int) // unbuffered
	ch <- 1                // blocks: no one is receiving, and never will be
}
```

---

## 31.6 Common Pitfall: Closing a Channel Twice, or from the Wrong Side

Closing an already-closed channel, or sending on a closed channel, both panic. Only the sender should close a channel, and only once — communicate closing intent clearly in your API design.

[Previous](./[30]-Concurrency-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[32]-Working-with-Files.md)
