[Previous](./[22]-Empty-Interface-and-Type-Assertions.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[24]-Constraints-Slices-and-Maps-Packages.md)

*Generics*

# Lesson 23 - Generics: Type Parameters And Constraints

## 23.1 Why Generics?

Before Go 1.18, writing a function that worked with multiple types meant either duplicating code per type or falling back to `any` and losing type safety. Generics let you write one function or type that works across many types, safely, checked at compile time.

---

## 23.2 Generic Functions

A type parameter is declared in square brackets before the regular parameter list.

```go
func Max[T int | float64](a, b T) T {
	if a > b {
		return a
	}
	return b
}

Max(3, 5)       // T inferred as int
Max(2.5, 1.1)   // T inferred as float64
```

---

## 23.3 Constraints

A constraint limits which types can be used for a type parameter — here, an inline union of allowed types.

```go
type Number interface {
	int | int64 | float64
}

func Sum[T Number](nums []T) T {
	var total T
	for _, n := range nums {
		total += n
	}
	return total
}
```

`any` is the loosest constraint — no restrictions, but also no operators like `+` or `<` are usable on the type parameter.

---

## 23.4 Generic Types

Types can also take type parameters, most usefully for containers.

```go
type Stack[T any] struct {
	items []T
}

func (s *Stack[T]) Push(item T) {
	s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
	var zero T
	if len(s.items) == 0 {
		return zero, false
	}
	last := s.items[len(s.items)-1]
	s.items = s.items[:len(s.items)-1]
	return last, true
}

s := Stack[int]{}
s.Push(1)
s.Push(2)
```

---

## 23.5 Explicit Type Arguments

Type arguments can usually be inferred, but can be specified explicitly when inference isn't possible.

```go
s := Stack[string]{}
result := Max[float64](1, 2)
```

[Previous](./[22]-Empty-Interface-and-Type-Assertions.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[24]-Constraints-Slices-and-Maps-Packages.md)
