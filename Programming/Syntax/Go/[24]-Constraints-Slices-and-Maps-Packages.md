[Previous](./[23]-Generics.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[25]-Goroutines.md)

*Generics*

# Lesson 24 - The constraints And slices/maps Packages

## 24.1 golang.org/x/constraints

Before built-in support stabilized, the `golang.org/x/constraints` package offered common reusable constraints like `constraints.Ordered` (anything supporting `<`, `<=`, `>`, `>=`).

```go
import "golang.org/x/exp/constraints"

func Max[T constraints.Ordered](a, b T) T {
	if a > b {
		return a
	}
	return b
}
```

Much of this has since been superseded by the standard library's `cmp` package (`cmp.Ordered`) — check current Go versions before adding the external dependency.

---

## 24.2 The slices Package

Standard-library generic helpers for working with slices (`slices`, stable since Go 1.21):

```go
import "slices"

nums := []int{3, 1, 2}
slices.Sort(nums)                 // [1 2 3]
slices.Contains(nums, 2)          // true
slices.Index(nums, 2)              // 1
slices.Reverse(nums)               // [3 2 1]
slices.Equal([]int{1,2}, []int{1,2}) // true
```

---

## 24.3 The maps Package

Standard-library generic helpers for maps:

```go
import "maps"

m := map[string]int{"a": 1, "b": 2}
keys := slices.Collect(maps.Keys(m))     // []string{"a", "b"} (order not guaranteed)
values := slices.Collect(maps.Values(m)) // []int{1, 2}
m2 := maps.Clone(m)                       // shallow copy
```

---

## 24.4 Why These Matter

Before these packages existed, sorting a slice or copying a map meant writing the same boilerplate loop in every project. Generic standard-library helpers remove that duplication while staying fully type-safe — no `any`, no type assertions, no runtime surprises.

[Previous](./[23]-Generics.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[25]-Goroutines.md)
