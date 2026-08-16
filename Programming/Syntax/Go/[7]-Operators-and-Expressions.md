[Previous](./[6]-Constants-and-iota.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators And Expressions

## 7.1 Arithmetic Operators

```go
+  -  *  /  %       // add, subtract, multiply, divide, modulo
```

Integer division truncates: `7 / 2` is `3`, not `3.5`. Divide floats to get fractional results.

---

## 7.2 Comparison and Logical Operators

```go
==  !=  <  <=  >  >=   // comparison, return bool
&&  ||  !                // logical AND, OR, NOT
```

Go has no ternary operator — an `if`/`else` expression is used instead (see Lesson 8).

---

## 7.3 Assignment Operators

```go
x := 5
x += 1   // x = x + 1
x -= 1
x *= 2
x /= 2
x %= 2
```

---

## 7.4 Increment and Decrement

```go
x++
x--
```

These are statements, not expressions — you can't write `y := x++`.

---

## 7.5 Bitwise Operators

```go
&   // AND
|   // OR
^   // XOR (also unary NOT)
&^  // AND NOT (bit clear)
<<  // left shift
>>  // right shift
```

[Previous](./[6]-Constants-and-iota.md) | [Table of Contents](./[0]-Introduction-to-Go.md) | [Next](./[8]-Conditionals.md)
