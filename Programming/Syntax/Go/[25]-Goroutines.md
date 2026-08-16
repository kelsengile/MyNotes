[Previous](./[24]-Constraints-Slices-and-Maps-Packages.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[26]-Channels.md)

*Concurrency*

# Lesson 25 - Goroutines

## 25.1 What Is a Goroutine?

A goroutine is a lightweight, independently-scheduled function managed by the Go runtime rather than the OS — you can spin up thousands of them cheaply, unlike OS threads.

```go
func sayHello() {
	fmt.Println("hello")
}

go sayHello() // runs concurrently, doesn't block the caller
```

---

## 25.2 The main Goroutine

Every program starts with one goroutine running `main()`. When `main()` returns, the program exits immediately — even if other goroutines haven't finished.

```go
func main() {
	go fmt.Println("might never print")
	// main() may return before the goroutine runs
}
```

This is why goroutines are almost always paired with synchronization (channels or `sync.WaitGroup`, covered in later lessons) to wait for completion.

---

## 25.3 Anonymous Goroutines and Capturing Variables

```go
for i := 0; i < 3; i++ {
	go func() {
		fmt.Println(i) // Go 1.22+: each iteration gets its own i
	}()
}
```

Passing loop variables as parameters is still a common, explicit alternative:

```go
for i := 0; i < 3; i++ {
	go func(n int) {
		fmt.Println(n)
	}(i)
}
```

---

## 25.4 Goroutines Are Not Free

Goroutines are cheap (a few KB of stack, growing as needed) but not zero-cost. Launching goroutines without bound — for example, one per incoming request with no limit — can still exhaust memory or overwhelm downstream systems. Concurrency patterns like worker pools (Lesson 30) manage this.

[Previous](./[24]-Constraints-Slices-and-Maps-Packages.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[26]-Channels.md)
