[Previous](./[26]-Channels.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[28]-Sync-Package.md)

*Concurrency*

# Lesson 27 - Select Statement

## 27.1 What select Does

`select` lets a goroutine wait on multiple channel operations at once, proceeding with whichever one becomes ready first — the concurrent equivalent of `switch`.

```go
select {
case msg1 := <-ch1:
	fmt.Println("received", msg1)
case msg2 := <-ch2:
	fmt.Println("received", msg2)
}
```

If multiple cases are ready simultaneously, `select` picks one at random.

---

## 27.2 default: Non-Blocking Operations

A `default` case runs immediately if no channel operation is ready, turning a normally-blocking channel op into a non-blocking one.

```go
select {
case v := <-ch:
	fmt.Println("got", v)
default:
	fmt.Println("no value ready")
}
```

---

## 27.3 Timeouts with select and time.After

```go
select {
case result := <-resultCh:
	fmt.Println(result)
case <-time.After(2 * time.Second):
	fmt.Println("timed out")
}
```

---

## 27.4 Combining select with a Quit Channel

A common pattern: loop on `select`, listening for both work and a signal to stop.

```go
for {
	select {
	case job := <-jobs:
		process(job)
	case <-quit:
		return
	}
}
```

---

## 27.5 Empty select

`select {}` with no cases blocks forever — occasionally used deliberately to keep a program alive, though a proper synchronization mechanism is usually clearer.

[Previous](./[26]-Channels.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[28]-Sync-Package.md)
