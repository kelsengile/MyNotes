[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[11]-Variadic-Functions-and-Closures.md)

*Core Syntax*

# Lesson 10 - Functions

## 10.1 Declaring Functions

```go
func add(a int, b int) int {
	return a + b
}

// shared type for consecutive params
func multiply(a, b int) int {
	return a * b
}
```

---

## 10.2 Multiple Return Values

Go functions can return more than one value — most idiomatically a result and an `error`.

```go
func divide(a, b float64) (float64, error) {
	if b == 0 {
		return 0, errors.New("division by zero")
	}
	return a / b, nil
}

result, err := divide(10, 2)
if err != nil {
	log.Fatal(err)
}
```

---

## 10.3 Named Return Values

Naming return values documents intent and lets you use a bare `return`.

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return // returns x, y
}
```

Use named returns sparingly — they can make longer functions harder to follow.

---

## 10.4 Functions as Values

Functions are first-class values in Go: they can be assigned to variables, passed as arguments, and returned from other functions.

```go
var op func(int, int) int = add

func applyTwice(f func(int) int, x int) int {
	return f(f(x))
}
```

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[11]-Variadic-Functions-and-Closures.md)
