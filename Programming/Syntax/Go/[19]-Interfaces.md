[Previous](./[18]-Pointers.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[20]-Embedding-and-Composition.md)

*Interfaces & Composition*

# Lesson 19 - Interfaces And Structural Typing

## 19.1 Defining an Interface

An interface lists a set of method signatures. Any type that implements those methods satisfies the interface automatically.

```go
type Shape interface {
	Area() float64
}
```

---

## 19.2 Structural (Implicit) Typing

Unlike Java or C#, Go types never declare "implements Shape" — satisfaction is purely structural. If the methods match, the type qualifies, even across unrelated packages.

```go
type Circle struct{ Radius float64 }

func (c Circle) Area() float64 {
	return 3.14159 * c.Radius * c.Radius
}

var s Shape = Circle{Radius: 2} // Circle satisfies Shape implicitly
```

---

## 19.3 Interfaces as Function Parameters

Accepting an interface instead of a concrete type lets a function work with anything that satisfies the contract — the basis of most Go abstractions.

```go
func totalArea(shapes []Shape) float64 {
	total := 0.0
	for _, s := range shapes {
		total += s.Area()
	}
	return total
}
```

---

## 19.4 Small Interfaces Are Idiomatic

Go's standard library favors tiny, single-method interfaces (`io.Reader`, `io.Writer`, `fmt.Stringer`) over large ones — "the bigger the interface, the weaker the abstraction."

```go
type Stringer interface {
	String() string
}
```

---

## 19.5 nil Interfaces

An interface value is `nil` only when *both* its type and value are unset. A `nil` pointer stored in an interface is **not** itself a `nil` interface — a classic Go gotcha.

```go
var p *Circle       // nil pointer
var s Shape = p      // s has a type (*Circle) and a nil value
fmt.Println(s == nil) // false!
```

[Previous](./[18]-Pointers.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[20]-Embedding-and-Composition.md)
