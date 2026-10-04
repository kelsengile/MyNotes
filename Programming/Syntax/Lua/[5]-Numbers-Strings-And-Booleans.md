[Previous](./[4]-Variables-And-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[6]-Operators-And-Expressions.md)

*Core Syntax*

# Lesson 5 - Numbers, Strings & Booleans

This lesson looks closely at three of Lua's basic types: numbers, strings, and booleans, plus the special value `nil`. Understanding how they behave prevents many beginner surprises.

---

## 5.1 Integers and Floats (Lua 5.3+)

Since Lua 5.3, the `number` type has two **subtypes**:

- **Integer**: whole numbers, stored as 64-bit signed values (range about ±9.2 × 10¹⁸).
- **Float**: double-precision floating-point numbers (about 15 to 16 significant decimal digits).

```lua
print(math.type(10))     --> integer
print(math.type(10.0))   --> float
print(math.type(1e2))    --> float   (scientific notation always makes a float)
```

You usually do not need to care which is which; Lua converts between them when needed. Integers and floats with the same mathematical value are **equal** and are the same table key:

```lua
print(1 == 1.0)          --> true
local t = {}
t[1.0] = "a"
print(t[1])              --> a
```

Integer limits and overflow:

```lua
print(math.maxinteger)       --> 9223372036854775807
print(math.mininteger)       --> -9223372036854775808
print(math.maxinteger + 1 == math.mininteger)   --> true (wraps around)
```

Integer arithmetic wraps around on overflow instead of raising an error. If you need larger numbers, use floats (`1e30`).

**Version note:** In Lua 5.1 and 5.2, *all* numbers are floats, so there is no integer subtype and `math.type` does not exist. LuaJIT also uses only doubles.

---

## 5.2 Arithmetic with Numbers

The result type follows simple rules:

- `+`, `-`, `*`, `%`, `//`: if both operands are integers, the result is an integer; otherwise it is a float.
- `/` (division) and `^` (exponent) **always** produce floats.

```lua
print(7 + 2)     --> 9
print(7 / 2)     --> 3.5
print(8 / 2)     --> 4.0     (float, even though it is a whole number)
print(7 // 2)    --> 3       (floor division)
print(7.0 // 2)  --> 3.0
print(-7 // 2)   --> -4      (rounds toward negative infinity)
print(7 % 3)     --> 1
print(-7 % 3)    --> 2       (result has the sign of the divisor)
print(2 ^ 10)    --> 1024.0
```

**Division by zero:**

```lua
print(1 / 0)     --> inf
print(-1 / 0)    --> -inf
print(0 / 0)     --> -nan or nan (platform-dependent; "not a number")
-- print(1 // 0) -- error: attempt to perform 'n//0'
-- print(1 % 0)  -- error: attempt to perform 'n%%0'
```

Floating division by zero gives infinity or NaN. Integer division or modulo by zero raises an error.

**Floating-point precision.** Floats are binary approximations, so some decimals are not exact:

```lua
print(0.1 + 0.2 == 0.3)    --> false
print(0.1 + 0.2)           --> 0.3  (print shows 14 digits)
print(string.format("%.17g", 0.1 + 0.2))  --> 0.30000000000000004
```

When comparing floats, check that they are *close enough* instead of exactly equal:

```lua
local function approx(a, b, eps)
  return math.abs(a - b) < (eps or 1e-9)
end
print(approx(0.1 + 0.2, 0.3))   --> true
```

NaN is never equal to anything, including itself, which gives a way to detect it: `x ~= x` is true only for NaN.

---

## 5.3 Number Formats (Hex, Scientific Notation)

Numbers can be written in several ways:

```lua
print(255)        --> 255          decimal integer
print(0xFF)       --> 255          hexadecimal integer
print(0xff)       --> 255          (letters can be either case)
print(3.14)       --> 3.14         decimal float
print(.5)         --> 0.5          (leading digit optional)
print(5.)         --> 5.0          (trailing digit optional)
print(1e3)        --> 1000.0       scientific: 1 x 10^3
print(2.5e-3)     --> 0.0025
print(1E2)        --> 100.0
print(0x10p2)     --> 64.0         hexadecimal float: 0x10 x 2^2
```

Hex integer literals that exceed the 64-bit range **wrap around** rather than becoming floats:

```lua
print(0xFFFFFFFFFFFFFFFF)   --> -1
```

Converting to and from other bases uses `tonumber` with a base, and `string.format`:

```lua
print(tonumber("ff", 16))          --> 255
print(string.format("%x", 255))    --> ff
print(string.format("%X", 255))    --> FF
print(string.format("%o", 8))      --> 10
```

Underscores as digit separators (`1_000_000`) are **not** allowed in Lua.

---

## 5.4 Strings: Creation and Basics (Quotes, Long Strings `[[ ]]`)

A **string** is a sequence of bytes. Lua strings can contain any byte, including zeros, and they are not tied to any particular text encoding (see Lesson 32 for UTF-8).

**Quotes.** Single and double quotes are identical in meaning; use whichever avoids escaping:

```lua
local a = "He said 'hello'"
local b = 'She said "hi"'
local c = "Line with an escaped \"quote\""
print(a, b, c)
```

**Long strings** use `[[ ... ]]`. They can span multiple lines and do **not** process escape sequences:

```lua
local poem = [[
Roses are red,
Violets are blue.\n is not processed here.
]]
print(poem)
```

A newline right after the opening `[[` is skipped, which lets you start the text on its own line. If your text contains `]]`, add equal signs between the brackets:

```lua
local s = [==[He said [[nested]] brackets.]==]
print(s)   --> He said [[nested]] brackets.
```

**Length and concatenation:**

```lua
local greeting = "Hello"
print(#greeting)                  --> 5   (number of bytes)
print(greeting .. ", World!")     --> Hello, World!
print("Score: " .. 42)            --> Score: 42   (number converted)
```

**Comparing strings** uses `==` for equality and `<`, `>` for alphabetical (byte/locale) order:

```lua
print("abc" == "abc")   --> true
print("a" < "b")        --> true
print("Z" < "a")        --> true  (uppercase letters come before lowercase in ASCII)
```

---

## 5.5 Escape Sequences

Inside quoted strings, a backslash starts an **escape sequence**:

| Sequence | Meaning |
|---|---|
| `\n` | Newline |
| `\t` | Tab |
| `\r` | Carriage return |
| `\\` | Backslash |
| `\"` | Double quote |
| `\'` | Single quote |
| `\a` | Bell (alert) |
| `\b` | Backspace |
| `\f` | Form feed |
| `\v` | Vertical tab |
| `\ddd` | Byte by decimal value (up to 3 digits), e.g. `\65` is `A` |
| `\xXX` | Byte by two hex digits, e.g. `\x41` is `A` |
| `\u{XXX}` | Unicode code point encoded as UTF-8 (Lua 5.3+), e.g. `\u{20AC}` is `€` |
| `\z` | Skips the following whitespace, including newlines |
| `\` + real newline | Inserts a newline |

```lua
print("Line1\nLine2")
print("Col1\tCol2")
print("C:\\Users\\me")
print("\65\66\67")          --> ABC
print("\x48\x69")           --> Hi
print("\u{48}\u{20AC}")     --> H€
```

`\z` is useful for splitting a long string across source lines without including the line breaks:

```lua
local long = "This is a long \z
              sentence written over two lines."
print(long)   --> This is a long sentence written over two lines.
```

Unknown escapes like `"\q"` cause a syntax error.

---

## 5.6 String Immutability and Interning

Strings in Lua are **immutable**: once created, they never change. Operations that seem to modify a string actually return a **new** one:

```lua
local s = "hello"
local t = s:upper()
print(s)   --> hello  (unchanged)
print(t)   --> HELLO
-- s[1] = "H"  -- not possible; strings cannot be modified in place
```

To "change" a string, build a new one and assign it back:

```lua
s = "J" .. s:sub(2)
print(s)   --> Jello
```

**Interning.** Lua stores each distinct *short* string (40 bytes or fewer, in the standard implementation) only once and reuses it. This means:

- Comparing short strings for equality is extremely fast (it compares references, not characters).
- Using strings as table keys is efficient.
- Creating many strings costs memory and garbage-collection time, so avoid building long strings with repeated `..` inside loops (use `table.concat`; see Lessons 13 and 15).

Interning is an implementation detail, so never depend on it for correctness; equality with `==` works the same for strings of any length.

---

## 5.7 Booleans and Truthiness (Only `nil` and `false` are False)

The `boolean` type has two values: `true` and `false`.

In a condition, Lua treats exactly two values as false: **`false`** and **`nil`**. *Everything else is true*, including values that other languages consider false:

```lua
local function check(v)
  if v then print(tostring(v) .. " is truthy")
  else      print(tostring(v) .. " is falsy") end
end

check(true)    --> true is truthy
check(false)   --> false is falsy
check(nil)     --> nil is falsy
check(0)       --> 0 is truthy      (!)
check("")      -->  is truthy       (!)  (empty string)
check({})      --> table: 0x... is truthy (!)  (empty table)
```

This is a common surprise for programmers coming from C, Python, or JavaScript. `if count then` checks whether `count` has a value at all, not whether it is non-zero. To test for zero, write `if count == 0 then`.

The logical operators `and`, `or`, and `not` are explained in Lesson 6. `not` always returns a real boolean:

```lua
print(not nil)    --> true
print(not 0)      --> false
print(not not "x")  --> true   (a common idiom to convert any value to a boolean)
```

---

## 5.8 `nil`: The Absence of a Value

`nil` is a type with a single value, also called `nil`. It means "no value". You encounter it when:

- Reading a variable that was never set.
- Reading a table key that does not exist.
- Calling a function that returns nothing but you use its result.
- A function such as `tonumber` fails.

```lua
local t = { a = 1 }
print(t.b)            --> nil
print(tonumber("x"))  --> nil
local v
print(v)              --> nil
```

Assigning `nil` to a **table field removes it** (Lesson 13). Assigning `nil` to a variable simply lets the old value be garbage-collected if nothing else uses it.

`nil` cannot be used as a table key, and arithmetic or concatenation with `nil` raises an error, one of the most common beginner errors:

```lua
local name
print(pcall(function() return "Hi " .. name end))
--> false	input:2: attempt to concatenate a nil value (upvalue 'name')
```

`nil` is not the same as `false`, though both are falsy:

```lua
print(nil == false)   --> false
```

Use `x == nil` when you must distinguish "missing" from `false`. For example, a setting that is explicitly `false` should not be overwritten by a default.

---

[Previous](./[4]-Variables-And-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[6]-Operators-And-Expressions.md)
