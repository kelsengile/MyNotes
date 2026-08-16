[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[10]-Functions.md)

*Core Syntax*

# Lesson 9 - Loops

## 9.1 for Is Go's Only Loop

Go has no `while` or `do-while` keyword — `for` covers every looping case.

```go
// classic three-part loop
for i := 0; i < 5; i++ {
	fmt.Println(i)
}

// while-style
n := 0
for n < 5 {
	n++
}

// infinite loop
for {
	// break out explicitly
	break
}
```

---

## 9.2 range

`range` iterates over slices, arrays, maps, strings, and channels.

```go
nums := []int{10, 20, 30}
for i, v := range nums {
	fmt.Println(i, v) // index, value
}

m := map[string]int{"a": 1}
for k, v := range m {
	fmt.Println(k, v)
}
```

Use `_` to discard a value you don't need:

```go
for _, v := range nums {
	fmt.Println(v)
}
```

---

## 9.3 break and continue

```go
for i := 0; i < 10; i++ {
	if i == 3 {
		continue // skip this iteration
	}
	if i == 6 {
		break // exit the loop
	}
	fmt.Println(i)
}
```

---

## 9.4 Labeled Loops

Labels let `break`/`continue` target an outer loop from inside a nested one.

```go
outer:
for i := 0; i < 3; i++ {
	for j := 0; j < 3; j++ {
		if j == 1 {
			continue outer
		}
		fmt.Println(i, j)
	}
}
```

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[10]-Functions.md)
