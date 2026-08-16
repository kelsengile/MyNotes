[Previous](./[17]-Structs.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[19]-Interfaces.md)

*Data Structures*

# Lesson 18 - Pointers

## 18.1 What Is a Pointer?

A pointer holds the memory address of a value rather than the value itself. Go has pointers but no pointer arithmetic, making them much safer than in C.

```go
x := 10
p := &x       // p is *int, holding x's address
fmt.Println(*p) // 10, dereferencing p
*p = 20
fmt.Println(x)  // 20 — x was modified through p
```

---

## 18.2 & and *

- `&x` — the "address of" operator, produces a pointer to `x`.
- `*p` — the "dereference" operator, accesses the value a pointer points to.
- `*int` — a *type*: "pointer to int".

---

## 18.3 Pointers to Structs

```go
type Point struct{ X, Y int }

p := &Point{X: 1, Y: 2}
p.X = 10 // Go auto-dereferences; no need to write (*p).X
```

---

## 18.4 Pointers and Function Arguments

Go passes arguments by value — a function receives a *copy*. Passing a pointer lets a function modify the caller's original data.

```go
func double(n int) {
	n *= 2 // only changes the local copy
}

func doubleViaPointer(n *int) {
	*n *= 2 // modifies the original
}

x := 5
doubleViaPointer(&x)
fmt.Println(x) // 10
```

---

## 18.5 nil Pointers

The zero value of any pointer type is `nil`. Dereferencing a `nil` pointer panics at runtime.

```go
var p *int
fmt.Println(p == nil) // true
// *p would panic: nil pointer dereference
```

---

## 18.6 new vs Composite Literals

`new(T)` allocates zeroed memory for a `T` and returns `*T`. In practice, `&T{}` is more common and more flexible since it lets you initialize fields inline.

```go
p1 := new(int)     // *int, pointing to 0
p2 := &Point{X: 1} // *Point, pointing to an initialized struct
```

[Previous](./[17]-Structs.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[19]-Interfaces.md)
