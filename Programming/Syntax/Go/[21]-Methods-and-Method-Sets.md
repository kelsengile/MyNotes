[Previous](./[20]-Embedding-and-Composition.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[22]-Empty-Interface-and-Type-Assertions.md)

*Interfaces & Composition*

# Lesson 21 - Methods And Method Sets

## 21.1 Declaring Methods

A method is a function with a special *receiver* argument, binding it to a type.

```go
type Rectangle struct {
	Width, Height float64
}

func (r Rectangle) Area() float64 {
	return r.Width * r.Height
}

rect := Rectangle{Width: 3, Height: 4}
fmt.Println(rect.Area()) // 12
```

---

## 21.2 Value Receivers vs Pointer Receivers

A **value receiver** operates on a copy; a **pointer receiver** operates on the original and can mutate it.

```go
func (r Rectangle) Scaled(factor float64) Rectangle { // value receiver
	r.Width *= factor
	r.Height *= factor
	return r // returns a new value; original untouched
}

func (r *Rectangle) Scale(factor float64) { // pointer receiver
	r.Width *= factor
	r.Height *= factor
}

rect.Scale(2) // mutates rect directly
```

---

## 21.3 Choosing a Receiver Type

- Use a **pointer receiver** if the method needs to modify the receiver, or the struct is large (avoiding copies).
- Use a **value receiver** for small, immutable-style types where copying is cheap and mutation isn't needed.
- Be consistent: if any method on a type uses a pointer receiver, it's idiomatic for all of that type's methods to do the same.

---

## 21.4 Method Sets and Interface Satisfaction

This is where receiver choice has real consequences: a type `T`'s method set includes only value-receiver methods, while `*T`'s method set includes both value- and pointer-receiver methods.

```go
type Sizer interface {
	Scale(factor float64)
}

var s Sizer = rect        // fails to compile: Rectangle doesn't have Scale
var s2 Sizer = &rect       // works: *Rectangle has Scale
```

If a type needs to satisfy an interface via a pointer-receiver method, you must use a pointer value to do so.

[Previous](./[20]-Embedding-and-Composition.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[22]-Empty-Interface-and-Type-Assertions.md)
