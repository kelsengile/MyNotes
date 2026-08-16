[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Constants And iota

## 6.1 Declaring Constants

Constants are fixed at compile time and can never change during execution.

```go
const Pi = 3.14159
const Greeting string = "hello"

const (
	StatusOK       = 200
	StatusNotFound = 404
)
```

---

## 6.2 Untyped Constants

A constant declared without a type is "untyped" and adopts whatever type it's used in, within limits:

```go
const Big = 1 << 62   // untyped, fits into int64 or float64 as needed
var f float64 = Big
var i int64 = Big
```

---

## 6.3 iota

`iota` is a counter that resets to `0` at each `const` block and increments by one per line — Go's idiomatic way to build enumerations.

```go
type Weekday int

const (
	Sunday Weekday = iota // 0
	Monday                // 1
	Tuesday                // 2
	Wednesday               // 3
)
```

---

## 6.4 iota with Expressions

`iota` can be used in expressions, which is handy for bit flags or unit conversions.

```go
const (
	_  = iota // skip 0
	KB = 1 << (10 * iota) // 1 << 10
	MB                     // 1 << 20
	GB                     // 1 << 30
)
```

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[7]-Operators-and-Expressions.md)
