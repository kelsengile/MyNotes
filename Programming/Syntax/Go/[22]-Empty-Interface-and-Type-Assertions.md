[Previous](./[21]-Methods-and-Method-Sets.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[23]-Generics.md)

*Interfaces & Composition*

# Lesson 22 - The Empty Interface And Type Assertions

## 22.1 The Empty Interface

`interface{}` (or its alias `any`, since Go 1.18) has zero methods, so *every* type satisfies it. It's Go's way of accepting a value of unknown type.

```go
func describe(v any) {
	fmt.Printf("value: %v, type: %T\n", v, v)
}

describe(42)
describe("hello")
describe(true)
```

Use `any` sparingly — it discards type safety, which generics (Lesson 23) now handle better in many cases.

---

## 22.2 Type Assertions

A type assertion extracts the concrete value stored inside an interface.

```go
var v any = "hello"

s := v.(string)          // panics if v isn't a string
s, ok := v.(string)       // safe form: ok is false instead of panicking
```

Always prefer the two-value form unless you're certain of the type.

---

## 22.3 Type Switches

A type switch runs different code depending on the interface's concrete type.

```go
func describe(v any) {
	switch x := v.(type) {
	case int:
		fmt.Println("int:", x)
	case string:
		fmt.Println("string:", x)
	case bool:
		fmt.Println("bool:", x)
	default:
		fmt.Printf("unknown type: %T\n", x)
	}
}
```

---

## 22.4 When to Reach for any

Use `any` for genuinely heterogeneous data (like decoding arbitrary JSON) or interop with reflection-based APIs. For everything else — especially container types like slices and maps of a known kind — prefer concrete types or generics.

[Previous](./[21]-Methods-and-Method-Sets.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[23]-Generics.md)
