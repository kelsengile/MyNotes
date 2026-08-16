[Previous](./[25]-Goroutines.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[27]-Select-Statement.md)

*Concurrency*

# Lesson 26 - Channels

## 26.1 What Is a Channel?

A channel is a typed pipe goroutines use to send and receive values, providing safe communication without manual locking — Go's philosophy: "don't communicate by sharing memory; share memory by communicating."

```go
ch := make(chan int)
go func() {
	ch <- 42 // send
}()
value := <-ch // receive
```

---

## 26.2 Unbuffered Channels

An unbuffered channel (`make(chan int)`) has no capacity — a send blocks until another goroutine is ready to receive, synchronizing the two goroutines at that point.

```go
done := make(chan bool)
go func() {
	fmt.Println("working...")
	done <- true
}()
<-done // blocks until the goroutine sends
```

---

## 26.3 Buffered Channels

A buffered channel (`make(chan int, 3)`) holds a fixed number of values without a receiver present. Sends only block once the buffer is full.

```go
ch := make(chan int, 2)
ch <- 1
ch <- 2
// ch <- 3 would block here — buffer is full
```

---

## 26.4 Closing Channels

`close(ch)` signals that no more values will be sent. Receiving from a closed channel returns the zero value immediately instead of blocking, and the comma-ok form tells you whether the channel is closed.

```go
close(ch)
v, ok := <-ch // ok is false once the channel is closed and drained
```

Only the sender should close a channel, and never close an already-closed channel — both cause a panic.

---

## 26.5 Ranging Over a Channel

```go
ch := make(chan int, 3)
ch <- 1
ch <- 2
ch <- 3
close(ch)

for v := range ch {
	fmt.Println(v) // 1, 2, 3, then the loop exits automatically
}
```

---

## 26.6 Directional Channels

Function signatures can restrict a channel to send-only or receive-only, making intent explicit and catching misuse at compile time.

```go
func producer(out chan<- int) { out <- 1 }
func consumer(in <-chan int) { fmt.Println(<-in) }
```

[Previous](./[25]-Goroutines.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[27]-Select-Statement.md)
