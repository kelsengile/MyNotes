[Previous](./[10]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[12]-String-Formatting.md)

*Core Syntax*

# Lesson 11 - Variadic Functions And Closures

## 11.1 Variadic Functions

A parameter prefixed with `...` accepts zero or more arguments, which arrive inside the function as a slice.

```go
func sum(nums ...int) int {
	total := 0
	for _, n := range nums {
		total += n
	}
	return total
}

sum(1, 2, 3)     // 6
sum()             // 0
```

Spread an existing slice into a variadic call with `...`:

```go
nums := []int{1, 2, 3}
sum(nums...)
```

`fmt.Println` is itself variadic, which is why it accepts any number of arguments.

---

## 11.2 Anonymous Functions

Functions can be declared inline, without a name, and called immediately or stored in a variable.

```go
square := func(x int) int {
	return x * x
}
fmt.Println(square(4)) // 16

func() {
	fmt.Println("runs immediately")
}()
```

---

## 11.3 Closures

A closure is a function that captures variables from its surrounding scope, keeping them alive as long as the closure exists.

```go
func counter() func() int {
	count := 0
	return func() int {
		count++
		return count
	}
}

next := counter()
fmt.Println(next()) // 1
fmt.Println(next()) // 2
```

Each call to `counter()` creates a fresh, independent `count`.

---

## 11.4 Common Closure Pitfall: Loop Variables

Before Go 1.22, loop variables were reused across iterations, a frequent source of bugs when capturing them in goroutines or closures. Go 1.22+ gives each iteration its own copy, but it's worth recognizing the pattern in older code:

```go
// Go 1.22+: each closure captures its own i
for i := 0; i < 3; i++ {
	go func() { fmt.Println(i) }()
}
```

[Previous](./[10]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[12]-String-Formatting.md)
