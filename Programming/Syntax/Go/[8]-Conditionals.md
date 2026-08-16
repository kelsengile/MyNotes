[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals

## 8.1 if / else

Go's `if` doesn't require parentheses around the condition, but braces are mandatory.

```go
if age >= 18 {
	fmt.Println("adult")
} else if age >= 13 {
	fmt.Println("teen")
} else {
	fmt.Println("child")
}
```

---

## 8.2 if with an Init Statement

`if` can run a short statement before the condition, scoping any declared variable to the `if`/`else` blocks only.

```go
if err := doSomething(); err != nil {
	log.Fatal(err)
}
```

This pattern is everywhere in idiomatic Go, especially for error handling.

---

## 8.3 switch

Go's `switch` doesn't fall through by default, so you don't need `break` statements.

```go
switch day {
case "Sat", "Sun":
	fmt.Println("weekend")
default:
	fmt.Println("weekday")
}
```

---

## 8.4 switch Without a Condition

A `switch` with no expression is a clean alternative to long `if`/`else if` chains.

```go
switch {
case age < 13:
	fmt.Println("child")
case age < 18:
	fmt.Println("teen")
default:
	fmt.Println("adult")
}
```

---

## 8.5 fallthrough

Explicitly fall into the next case with `fallthrough` when you actually want the old C-style behavior.

```go
switch n := 1; n {
case 1:
	fmt.Println("one")
	fallthrough
case 2:
	fmt.Println("two")
}
// prints "one" then "two"
```

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[9]-Loops.md)
