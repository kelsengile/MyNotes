[Previous](./[14]-Panic-Recover-and-Defer.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[16]-Maps.md)

*Data Structures*

# Lesson 15 - Arrays And Slices

## 15.1 Arrays

An array has a fixed size that's part of its type — `[3]int` and `[5]int` are different types entirely.

```go
var a [3]int              // [0 0 0]
b := [3]int{1, 2, 3}
c := [...]int{1, 2, 3, 4} // size inferred: [4]int
```

Arrays are rarely used directly in idiomatic Go; slices are far more common.

---

## 15.2 Slices

A slice is a flexible, growable view over an underlying array — this is what you'll use for lists of data in practice.

```go
s := []int{1, 2, 3}       // slice literal
s2 := make([]int, 5)       // length 5, zero-valued
s3 := make([]int, 0, 10)   // length 0, capacity 10
```

---

## 15.3 Appending and Growing

`append` adds elements, growing the underlying array (and reallocating) when capacity is exceeded.

```go
s := []int{1, 2}
s = append(s, 3)         // [1 2 3]
s = append(s, 4, 5)      // [1 2 3 4 5]

other := []int{6, 7}
s = append(s, other...)  // spread another slice in
```

`append` always returns a (possibly new) slice — never discard the result.

---

## 15.4 Slicing Syntax

```go
s := []int{0, 1, 2, 3, 4, 5}
s[1:4]   // [1 2 3]  (index 1 up to, not including, 4)
s[:3]    // [0 1 2]
s[3:]    // [3 4 5]
```

---

## 15.5 len, cap, and Shared Backing Arrays

`len` is the number of elements; `cap` is how far the slice can grow before reallocating. Slices derived from the same array share memory — mutating one can affect the other.

```go
a := []int{1, 2, 3, 4, 5}
b := a[1:3]
b[0] = 99
fmt.Println(a) // [1 99 3 4 5] — a is affected too
```

Use `copy` to get an independent slice:

```go
dst := make([]int, len(a))
copy(dst, a)
```

[Previous](./[14]-Panic-Recover-and-Defer.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[16]-Maps.md)
