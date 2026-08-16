[Previous](./[27]-Select-Statement.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[29]-Context-Package.md)

*Concurrency*

# Lesson 28 - sync Package

## 28.1 sync.WaitGroup

A `WaitGroup` blocks until a collection of goroutines finishes — the standard way to wait for concurrent work to complete.

```go
var wg sync.WaitGroup

for i := 0; i < 3; i++ {
	wg.Add(1)
	go func(n int) {
		defer wg.Done()
		fmt.Println(n)
	}(i)
}

wg.Wait() // blocks until all 3 call Done()
```

`Add` before launching each goroutine, `Done` (usually via `defer`) when it finishes, `Wait` to block until the count reaches zero.

---

## 28.2 sync.Mutex

A `Mutex` protects shared data from concurrent access, ensuring only one goroutine touches it at a time.

```go
var mu sync.Mutex
var counter int

func increment() {
	mu.Lock()
	defer mu.Unlock()
	counter++
}
```

Without the lock, concurrent increments race and produce incorrect results — see Lesson 31 on the race detector.

---

## 28.3 sync.RWMutex

`RWMutex` allows any number of concurrent readers, or one exclusive writer — useful when reads vastly outnumber writes.

```go
var mu sync.RWMutex
var data map[string]string

func read(key string) string {
	mu.RLock()
	defer mu.RUnlock()
	return data[key]
}

func write(key, value string) {
	mu.Lock()
	defer mu.Unlock()
	data[key] = value
}
```

---

## 28.4 sync.Once

`Once` guarantees a function runs exactly once, no matter how many goroutines call it — a clean way to handle one-time initialization.

```go
var once sync.Once

func setup() {
	once.Do(func() {
		fmt.Println("initializing...")
	})
}
```

---

## 28.5 sync.Map

A concurrency-safe map, useful in specific high-contention scenarios (many goroutines, disjoint keys) — for most cases, a regular `map` with a `Mutex` is simpler and just as effective.

```go
var m sync.Map
m.Store("key", 42)
value, ok := m.Load("key")
```

[Previous](./[27]-Select-Statement.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[29]-Context-Package.md)
