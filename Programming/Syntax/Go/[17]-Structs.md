[Previous](./[16]-Maps.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[18]-Pointers.md)

*Data Structures*

# Lesson 17 - Structs

## 17.1 Defining and Creating Structs

A struct groups related fields into a single type — Go's equivalent of a lightweight class without inheritance.

```go
type Person struct {
	Name string
	Age  int
}

p := Person{Name: "Ada", Age: 30}
p2 := Person{"Ada", 30} // positional, order-dependent — less preferred
var p3 Person             // zero value: {"" 0}
```

---

## 17.2 Accessing and Modifying Fields

```go
fmt.Println(p.Name)
p.Age = 31
```

---

## 17.3 Nested Structs

```go
type Address struct {
	City string
}

type Employee struct {
	Person
	Address Address
	Salary  int
}

e := Employee{
	Person:  Person{Name: "Ada", Age: 30},
	Address: Address{City: "London"},
}
```

---

## 17.4 Struct Comparison

Structs are comparable with `==` if every field is itself comparable (no slices, maps, or funcs).

```go
p1 := Person{"Ada", 30}
p2 := Person{"Ada", 30}
fmt.Println(p1 == p2) // true
```

---

## 17.5 Struct Tags

Tags are string metadata attached to fields, read via reflection — most commonly used by encoding packages like `encoding/json`.

```go
type User struct {
	Name string `json:"name"`
	Age  int    `json:"age,omitempty"`
}
```

[Previous](./[16]-Maps.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[18]-Pointers.md)
