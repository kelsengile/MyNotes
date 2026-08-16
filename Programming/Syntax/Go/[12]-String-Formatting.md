[Previous](./[11]-Variadic-Functions-and-Closures.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[13]-Error-Handling.md)

*Core Syntax*

# Lesson 12 - String Formatting

## 12.1 The fmt Package

```go
fmt.Println("hello")            // print with newline
fmt.Print("hello")               // print without newline
fmt.Printf("%s is %d\n", "Ada", 30) // formatted print
s := fmt.Sprintf("%s is %d", "Ada", 30) // format into a string
```

Common verbs: `%s` (string), `%d` (int), `%f` (float), `%v` (default format for any value), `%T` (type), `%t` (bool), `%q` (quoted string).

---

## 12.2 The strings Package

```go
strings.ToUpper("go")        // "GO"
strings.Contains("golang", "lang") // true
strings.Split("a,b,c", ",")   // []string{"a", "b", "c"}
strings.Join([]string{"a","b"}, "-") // "a-b"
strings.TrimSpace("  hi  ")   // "hi"
strings.Replace("aaa", "a", "b", 1) // "baa"
```

For heavy string building, use `strings.Builder` — it avoids repeated allocations:

```go
var b strings.Builder
b.WriteString("Hello, ")
b.WriteString("Go!")
fmt.Println(b.String())
```

---

## 12.3 The strconv Package

Converting between strings and other types:

```go
i, err := strconv.Atoi("42")       // string -> int
s := strconv.Itoa(42)               // int -> string
f, err := strconv.ParseFloat("3.14", 64)
b, err := strconv.ParseBool("true")
```

Always check the returned `error` — a malformed string (like `"abc"` to `Atoi`) fails at runtime, not compile time.

---

## 12.4 Strings Are UTF-8 Byte Sequences

A Go `string` is an immutable sequence of bytes, conventionally UTF-8 encoded. Indexing gives you a byte, not necessarily a character — for multi-byte runes, `range` gives you the correct rune boundaries.

```go
s := "héllo"
fmt.Println(len(s))          // 6 (bytes, not characters)
for i, r := range s {
	fmt.Println(i, string(r)) // correct rune-by-rune iteration
}
```

[Previous](./[11]-Variadic-Functions-and-Closures.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[13]-Error-Handling.md)
