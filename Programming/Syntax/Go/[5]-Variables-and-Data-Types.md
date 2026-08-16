[Previous](./[4]-Workspace-Layout-and-Project-Structure.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[6]-Constants-and-iota.md)

*Core Syntax*

# Lesson 5 - Variables And Data Types

## 5.1 Declaring Variables

Go is statically typed, but it can infer types for you.

```go
var name string = "Ada"   // explicit type
var age = 30              // inferred type
count := 0                // short declaration (inside functions only)
```

`:=` is shorthand for `var x = value` and can only be used inside function bodies, never at package level.

---

## 5.2 Zero Values

Every declared variable gets a sensible default if you don't initialize it — Go has no `undefined` or `null` for basic types.

```go
var i int       // 0
var f float64   // 0.0
var s string    // ""
var b bool      // false
```

---

## 5.3 Basic Types

```go
// Integers
int, int8, int16, int32, int64
uint, uint8, uint16, uint32, uint64

// Floating point
float32, float64

// Other
bool
string
byte    // alias for uint8
rune    // alias for int32, represents a Unicode code point
```

Use plain `int` unless you have a specific reason (memory layout, interop) to pick a sized variant.

---

## 5.4 Type Conversion

Go never converts types implicitly — you must convert explicitly, even between numeric types.

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

---

## 5.5 Multiple Declarations

```go
var (
	name string = "Ada"
	age  int    = 30
)

x, y := 1, 2
```

[Previous](./[4]-Workspace-Layout-and-Project-Structure.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[6]-Constants-and-iota.md)
