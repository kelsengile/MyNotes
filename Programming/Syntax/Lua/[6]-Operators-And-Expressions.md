[Previous](./[5]-Numbers-Strings-And-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[7]-Conditionals.md)

*Core Syntax*

# Lesson 6 - Operators & Expressions

Operators combine values into new values. This lesson covers every Lua operator, how they are grouped when several appear together, and a few idioms (like the `and`/`or` ternary) that replace features Lua leaves out on purpose.

---

## 6.1 Arithmetic Operators (`+ - * / // % ^`)

| Operator | Name | Example | Result |
|---|---|---|---|
| `+` | Addition | `5 + 3` | `8` |
| `-` | Subtraction | `5 - 3` | `2` |
| `*` | Multiplication | `5 * 3` | `15` |
| `/` | Float division | `5 / 2` | `2.5` |
| `//` | Floor division (Lua 5.3+) | `5 // 2` | `2` |
| `%` | Modulo (remainder) | `5 % 2` | `1` |
| `^` | Exponentiation | `2 ^ 3` | `8.0` |
| `-` (unary) | Negation | `-5` | `-5` |

```lua
print(10 / 4)     --> 2.5
print(10 // 4)    --> 2
print(10 % 4)     --> 2
print(2 ^ 0.5)    --> 1.4142135623731  (square root of 2)
print(-3 ^ 2)     --> -9.0   (^ binds tighter than unary minus)
print(5.5 // 2)   --> 2.0
print(-5 % 3)     --> 1      (modulo result takes the sign of the divisor)
```

`//` rounds **toward negative infinity** (`-7 // 2` is `-4`, not `-3`), and `a % b == a - (a // b) * b` always holds. `^` and `/` always return floats. Lesson 5.2 covers how integer and float types combine.

**Version note:** `//` was added in Lua 5.3. In 5.1 and 5.2 use `math.floor(a / b)`.

---

## 6.2 Relational Operators (`== ~= < > <= >=`)

| Operator | Meaning |
|---|---|
| `==` | Equal to |
| `~=` | Not equal to (note: **not** `!=`) |
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less than or equal |
| `>=` | Greater than or equal |

All of them return a boolean.

```lua
print(3 == 3.0)      --> true
print("a" ~= "b")    --> true
print(5 >= 5)        --> true
print("apple" < "banana")  --> true
```

**Equality rules:**

- Values of **different types** are never equal; `==` simply returns `false` (no error and no conversion): `1 == "1"` is `false`.
- Numbers compare by mathematical value (`1 == 1.0` is true).
- Strings compare by content.
- **Tables, functions, and threads** compare by *identity* (are they the very same object?), not by contents:

```lua
local a = { 1, 2 }
local b = { 1, 2 }
local c = a
print(a == b)   --> false  (different tables, even with equal contents)
print(a == c)   --> true   (same table)
```

(The `__eq` metamethod can customize this for tables; see Lesson 16.)

**Ordering rules:** `<`, `>`, `<=`, `>=` work on two numbers or two strings. Mixing types raises an error:

```lua
print(pcall(function() return 1 < "2" end))
--> false	input:1: attempt to compare number with string
```

---

## 6.3 Logical Operators (`and`, `or`, `not`) and Short-Circuiting

Lua has three logical operators. Remember that only `false` and `nil` count as false.

- `not x` returns `true` if `x` is falsy, otherwise `false`. It always returns a boolean.
- `a and b` returns `a` if `a` is falsy; otherwise it returns `b`.
- `a or b` returns `a` if `a` is truthy; otherwise it returns `b`.

Notice that `and` and `or` return **one of their operands**, not necessarily a boolean:

```lua
print(nil and 5)      --> nil
print(false and 5)    --> false
print(1 and 5)        --> 5
print(nil or "default")  --> default
print(0 or "default")    --> 0   (0 is truthy)
print(not nil)        --> true
print(not 5)          --> false
```

**Short-circuit evaluation.** The right side is evaluated **only if needed**:

```lua
local t = nil
if t and t.x > 0 then   -- safe: t.x is never evaluated when t is nil
  print("positive")
end

local function expensive() print("called"); return true end
print(true or expensive())    --> true   (expensive() is NOT called)
print(false and expensive())  --> false  (expensive() is NOT called)
```

Common idioms:

```lua
local name = user_input or "Anonymous"     -- default value
local value = t and t.field                -- safe nested access
```

---

## 6.4 The `and` / `or` Ternary Idiom and Its Pitfall

Lua has no `?:` ternary operator. The usual replacement is:

```lua
local result = condition and value_if_true or value_if_false
```

```lua
local age = 20
local label = age >= 18 and "adult" or "minor"
print(label)   --> adult
```

**The pitfall:** it works only when `value_if_true` is itself truthy. If it is `false` or `nil`, the expression falls through to the `or` part, even when the condition is true:

```lua
local cond = true
local r = cond and false or "fallback"
print(r)   --> fallback   (WRONG: we wanted false)
```

Safe alternatives:

```lua
-- 1. Use a normal if statement
local r
if cond then r = false else r = "fallback" end

-- 2. Wrap the value in a helper function
local function choose(c, a, b)
  if c then return a else return b end
end
print(choose(true, false, "fallback"))   --> false
```

Note that a helper function evaluates both `a` and `b` before the call, so it does not short-circuit. Use an `if` statement when evaluating the unused branch would be costly or unsafe.

---

## 6.5 String Concatenation (`..`)

The `..` operator joins strings. Numbers are converted automatically:

```lua
print("Hello" .. ", " .. "World")   --> Hello, World
print("Score: " .. 100)             --> Score: 100
print(1 .. 2)                       --> 12
print("pi = " .. 3.14)              --> pi = 3.14
```

Details:

- When writing a number literal next to `..`, **leave a space**: `1..2` is a malformed number error.
- Only strings and numbers can be concatenated. Anything else (`nil`, booleans, tables) raises an error unless you convert it with `tostring`:

```lua
print("flag: " .. tostring(true))   --> flag: true
print(pcall(function() return "x" .. nil end))
--> false	input:1: attempt to concatenate a nil value
```

- `..` is **right associative**: `"a" .. "b" .. "c"` is `"a" .. ("b" .. "c")`.
- Every concatenation creates a new string. Building a big string in a loop with `..` is slow; collect pieces in a table and use `table.concat` (Lesson 15).
- Floats keep their `.0`: `1.0 .. ""` gives `"1.0"`.

---

## 6.6 The Length Operator (`#`)

`#` returns the length of a string or table:

```lua
print(#"hello")         --> 5     (number of bytes, not characters)
print(#"héllo")         --> 6     (é takes 2 bytes in UTF-8)
print(#{ 10, 20, 30 })  --> 3
print(#{})              --> 0
```

For tables, `#` gives the length of the **sequence** (consecutive integer keys starting at 1). If the table has holes (`nil` values in the middle), the result can be any valid "border", so avoid `#` on tables with holes (Lesson 13).

Common uses:

```lua
local list = { "a", "b", "c" }
list[#list + 1] = "d"     -- append
print(list[#list])        --> d   (last element)
```

Remember that `#` on a string counts **bytes**; use the `utf8` library for characters (Lesson 32). The `__len` metamethod can override `#` for tables (Lesson 16).

---

## 6.7 Bitwise Operators (`& | ~ << >>`)

Introduced in Lua 5.3, bitwise operators work on **integers** (floats are converted if they have an exact integer value):

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `&` | AND | `5 & 3` | `1` |
| `\|` | OR | `5 \| 3` | `7` |
| `~` | XOR (binary) | `5 ~ 3` | `6` |
| `~` | NOT (unary) | `~5` | `-6` |
| `<<` | Shift left | `1 << 4` | `16` |
| `>>` | Shift right (logical, fills with zeros) | `16 >> 2` | `4` |

```lua
local flags = 0
local READ, WRITE = 1, 2      -- binary 01 and 10
flags = flags | READ | WRITE  -- set both
print(flags)                  --> 3
print(flags & WRITE ~= 0)     --> true  (works, but see 6.8)
print((flags & WRITE) ~= 0)   --> true  (clearer)
flags = flags & ~READ         -- clear the READ bit
print(flags)                  --> 2
```

Notes:

- Bitwise operators raise an error for floats without an integer value (`2.5 & 1`).
- The shift operators shift by whole bits; shifting by 64 or more gives `0`; negative shift counts shift the other way.
- Lesson 32 shows practical uses (flags and masks).

**Version note:** These operators do not exist in Lua 5.1/5.2. Lua 5.2 has a `bit32` library, and LuaJIT has a `bit` library (Lesson 32).

---

## 6.8 Operator Precedence

From **lowest** to **highest** precedence:

| Level | Operators | Associativity |
|---|---|---|
| 1 (lowest) | `or` | left |
| 2 | `and` | left |
| 3 | `<` `>` `<=` `>=` `~=` `==` | left |
| 4 | `\|` | left |
| 5 | `~` (binary XOR) | left |
| 6 | `&` | left |
| 7 | `<<` `>>` | left |
| 8 | `..` | **right** |
| 9 | `+` `-` | left |
| 10 | `*` `/` `//` `%` | left |
| 11 | unary `not` `#` `-` `~` | right |
| 12 (highest) | `^` | **right** |

Examples:

```lua
print(2 + 3 * 4)        --> 14    (* before +)
print((2 + 3) * 4)      --> 20    (parentheses override)
print(2 ^ 3 ^ 2)        --> 512.0 (right associative: 2^(3^2))
print(-2 ^ 2)           --> -4.0  (^ before unary minus)
print(not 1 == 2)       --> false (not applies first: (not 1) == 2)
print("a" .. "b" == "ab")   --> true (.. before ==)
print(1 .. 2 + 3)       --> 15    (+ before ..: 1 .. 5)
```

**Bitwise caution:** in Lua, the bitwise operators bind *tighter* than the comparison operators (the opposite of C). So `flags & WRITE ~= 0` is parsed as `(flags & WRITE) ~= 0`, which is what you usually want. Still, add explicit parentheses around bit operations so your intent is obvious to every reader (and to programmers used to C).

When in doubt, use parentheses. They cost nothing and make code easier to read.

---

## 6.9 Why There Is No `+=` or `++`

Lua has no compound assignment operators (`+=`, `-=`, `*=`) and no increment/decrement operators (`++`, `--`). (Note: `--` starts a comment!)

```lua
local count = 0
count = count + 1    -- the Lua way
count = count * 2
```

Why? In Lua, assignment is a **statement**, not an expression. It cannot appear inside another expression, so constructs like `x = y = 0` or `if (x = f())` do not exist. This keeps the grammar tiny and removes a whole class of bugs (such as writing `=` where `==` was intended, which is a syntax error in Lua).

If you write `count++` you get a syntax error. For repeated updates of a long expression, use a local helper:

```lua
local t = { stats = { hits = 0 } }
local s = t.stats          -- avoid repeating the long path
s.hits = s.hits + 1
print(t.stats.hits)        --> 1
```

**Version note:** Luau (Roblox) adds compound assignment (`+=`, `..=`, etc.), but standard Lua does not.

---

[Previous](./[5]-Numbers-Strings-And-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[7]-Conditionals.md)
