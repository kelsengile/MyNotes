[Previous](./[29]-Context-Package.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[31]-Race-Detector-and-Common-Pitfalls.md)

*Concurrency*

# Lesson 30 - Concurrency Patterns

## 30.1 Worker Pools

A fixed number of goroutines ("workers") pull jobs from a shared channel, bounding concurrency instead of launching one goroutine per job.

```go
func worker(id int, jobs <-chan int, results chan<- int) {
	for j := range jobs {
		results <- j * 2
	}
}

jobs := make(chan int, 100)
results := make(chan int, 100)

for w := 1; w <= 3; w++ {
	go worker(w, jobs, results)
}

for j := 1; j <= 5; j++ {
	jobs <- j
}
close(jobs)
```

---

## 30.2 Fan-Out, Fan-In

**Fan-out**: multiple goroutines read from the same channel to parallelize work. **Fan-in**: multiple channels are merged into one, combining results.

```go
func fanIn(chs ...<-chan int) <-chan int {
	out := make(chan int)
	var wg sync.WaitGroup
	for _, ch := range chs {
		wg.Add(1)
		go func(c <-chan int) {
			defer wg.Done()
			for v := range c {
				out <- v
			}
		}(ch)
	}
	go func() {
		wg.Wait()
		close(out)
	}()
	return out
}
```

---

## 30.3 Pipelines

A pipeline chains stages together, each a goroutine reading from an input channel and writing to an output channel — data flows through the pipeline concurrently.

```go
func generate(nums ...int) <-chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for _, n := range nums {
			out <- n
		}
	}()
	return out
}

func square(in <-chan int) <-chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for n := range in {
			out <- n * n
		}
	}()
	return out
}

for v := range square(generate(1, 2, 3)) {
	fmt.Println(v) // 1, 4, 9
}
```

---

## 30.4 Rate Limiting

Combine `time.Ticker` with a channel to throttle how quickly work is processed.

```go
limiter := time.Tick(200 * time.Millisecond)
for _, job := range jobs {
	<-limiter
	process(job)
}
```

[Previous](./[29]-Context-Package.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[31]-Race-Detector-and-Common-Pitfalls.md)
