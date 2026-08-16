[Previous](./[19]-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[21]-Methods-and-Method-Sets.md)

*Interfaces & Composition*

# Lesson 20 - Embedding And Composition

## 20.1 Go Has No Inheritance

Go deliberately omits class inheritance. Instead, it favors **composition**: building new types out of existing ones, and reusing behavior through embedding rather than an "is-a" hierarchy.

---

## 20.2 Struct Embedding

Embedding a type inside a struct (without a field name) promotes its fields and methods to the outer struct.

```go
type Animal struct {
	Name string
}

func (a Animal) Describe() string {
	return "I am " + a.Name
}

type Dog struct {
	Animal    // embedded, not a named field
	Breed string
}

d := Dog{Animal: Animal{Name: "Rex"}, Breed: "Labrador"}
fmt.Println(d.Name)       // promoted field
fmt.Println(d.Describe()) // promoted method
```

---

## 20.3 Overriding Promoted Methods

A method defined directly on the outer type shadows the embedded type's version of the same name.

```go
func (d Dog) Describe() string {
	return "Woof, I am " + d.Name
}
// d.Describe() now calls Dog's version, not Animal's
```

---

## 20.4 Interface Embedding

Interfaces can embed other interfaces, composing larger contracts from smaller ones — this is how `io.ReadWriter` is built from `io.Reader` and `io.Writer`.

```go
type Reader interface {
	Read(p []byte) (n int, err error)
}

type Writer interface {
	Write(p []byte) (n int, err error)
}

type ReadWriter interface {
	Reader
	Writer
}
```

---

## 20.5 Composition Over Inheritance in Practice

Prefer embedding small, focused types and interfaces over building deep hierarchies. Go's philosophy: assemble behavior from independent pieces rather than sharing it through a rigid parent-child chain.

[Previous](./[19]-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[21]-Methods-and-Method-Sets.md)
