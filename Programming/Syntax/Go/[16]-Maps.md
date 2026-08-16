[Previous](./[15]-Arrays-and-Slices.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[17]-Structs.md)

*Data Structures*

# Lesson 16 - Maps

## 16.1 Declaring and Initializing Maps

```go
m := map[string]int{"a": 1, "b": 2}
m2 := make(map[string]int) // empty, ready to use
var m3 map[string]int      // nil map — reads are fine, writes panic
```

---

## 16.2 Reading, Writing, and Deleting

```go
m["c"] = 3          // set
value := m["a"]      // read (1)
delete(m, "a")        // remove
```

Reading a missing key returns the value type's zero value — it does not error or panic.

---

## 16.3 The "Comma OK" Idiom

To distinguish "key present with zero value" from "key absent", use the two-value form:

```go
value, ok := m["missing"]
if !ok {
	fmt.Println("key not found")
}
```

---

## 16.4 Iterating Over Maps

```go
for key, value := range m {
	fmt.Println(key, value)
}
```

Map iteration order is intentionally randomized by Go — never rely on it. Sort the keys first if you need a stable order.

---

## 16.5 Maps as Sets

A common idiom is using `map[T]struct{}` (or `map[T]bool`) to represent a set, since `struct{}` occupies zero bytes.

```go
seen := make(map[string]struct{})
seen["go"] = struct{}{}
_, exists := seen["go"] // true
```

[Previous](./[15]-Arrays-and-Slices.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[17]-Structs.md)
