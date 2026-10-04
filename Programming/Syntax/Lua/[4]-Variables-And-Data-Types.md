[Previous](./[3]-LuaRocks-And-Packages.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[5]-Numbers-Strings-And-Booleans.md)

*Core Syntax*

# Lesson 4 - Variables & Basic Data Types

Variables are how a program remembers things. This lesson covers how to create and name variables, how Lua's eight basic types work, how to check and convert between them, and how to write comments and constants.

---

## 4.1 What is a Variable?

A **variable** is a name that refers to a value. You create one by assigning with `=`:

```lua
age = 30
name = "Ada"
print(name, age)   --> Ada	30
```

A few things to notice:

- There is **no keyword to declare the type** (no `int` or `string`).
- A variable can be reassigned to a value of a different type at any time.
- Reading a variable that was never assigned gives `nil`, not an error:

```lua
print(unknown)   --> nil
```

---

## 4.2 Naming Rules, Reserved Words & Conventions

**Rules for names (identifiers):**

- Use letters, digits, and underscores.
- Do not start with a digit.
- Names are **case-sensitive**: `Score`, `score`, and `SCORE` are three different variables.

```lua
local player1 = "ok"
local _unused = 0
local my_var = 1
-- local 1player = 1   -- INVALID: starts with a digit
-- local my-var = 1    -- INVALID: hyphen is the minus operator
```

**Reserved words** cannot be used as names. Lua 5.4 reserves these 22 words:

```text
and       break     do        else      elseif    end
false     for       function  goto      if        in
local     nil       not       or        repeat    return
then      true      until     while
```

Names starting with an underscore followed by uppercase letters (such as `_VERSION` and `_ENV`) are reserved for Lua's internal use. A lone `_` is a common convention for "a value I don't care about".

**Style conventions** (not enforced by Lua):

| Kind | Common style | Example |
|---|---|---|
| Variables and functions | `snake_case` or `camelCase` | `player_score` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_SPEED` |
| Classes/modules | `PascalCase` | `Vector` |
| Unused values | `_` | `for _, v in ipairs(t)` |

Pick one style and stay consistent. This course uses `snake_case`.

---

## 4.3 Comments (`--` and `--[[ ]]`)

Comments are notes for humans; Lua ignores them.

**Single-line comment** starts with two hyphens and runs to the end of the line:

```lua
-- This is a comment
local x = 5   -- This is also a comment
```

**Block (multi-line) comment** starts with `--[[` and ends with `]]`:

```lua
--[[
  This comment spans
  several lines.
]]
```

If your comment itself contains `]]`, add equal signs between the brackets. The opening and closing markers must use the same count:

```lua
--[==[
  This comment can safely contain ]] inside it.
]==]
```

**A handy trick** for toggling code on and off: write `---[[` (three hyphens) and the block becomes a single-line comment followed by live code. Remove one hyphen to disable it again:

```lua
---[[
print("this line runs")
--]]
```

---

## 4.4 Global vs Local Variables (`local`)

A variable is **global** by default. A variable declared with `local` exists only in the block where it is declared (a block is a function body, loop body, `if` body, `do ... end`, or a whole file).

```lua
x = 10          -- global: visible everywhere
local y = 20    -- local: visible only in this block and nested ones

do
  local z = 30
  print(y, z)   --> 20	30
end
print(z)        --> nil (z no longer exists)
```

**Always prefer `local`.** Globals are slower to access, they can be changed accidentally from anywhere, and misspelling a name silently creates a new global instead of raising an error:

```lua
local counter = 0
conter = counter + 1   -- typo creates a new global "conter"!
print(counter)         --> 0
```

Globals actually live in an ordinary table called `_G` (see Lessons 10 and 24).

You can declare a local without a value, and it starts as `nil`:

```lua
local result
print(result)   --> nil
```

---

## 4.5 Dynamic Typing

In Lua, **values** have types, **variables** do not. A variable can hold anything and change its mind:

```lua
local v = 42
print(v)          --> 42
v = "now a string"
print(v)          --> now a string
v = {1, 2, 3}
print(#v)         --> 3
```

Benefits: less ceremony and very flexible code. Cost: mistakes (like passing a string where a number is expected) are caught only when that line runs. Defensive checks (`type(x) == "number"`) and linting tools (Lesson 41) help.

---

## 4.6 The Eight Basic Types (`nil`, `boolean`, `number`, `string`, `function`, `table`, `userdata`, `thread`)

| Type | Description | Example |
|---|---|---|
| `nil` | The absence of a value; also the value of unassigned variables | `nil` |
| `boolean` | Logical true/false | `true`, `false` |
| `number` | Integers and floating-point numbers (two subtypes in 5.3+) | `42`, `3.14` |
| `string` | Immutable sequence of bytes | `"hello"` |
| `function` | Callable code; functions are values | `function() end` |
| `table` | The one data-structure type: arrays, dictionaries, objects | `{1, 2, x = 3}` |
| `userdata` | Raw memory/C data created by a host program or C library | file handles (`io.stdout`) |
| `thread` | A coroutine (independent line of execution; not an OS thread) | `coroutine.create(f)` |

```lua
local a = nil
local b = true
local c = 12
local d = 12.5
local e = "text"
local f = print
local g = {}
local h = io.stdout
local i = coroutine.create(function() end)
```

You will meet `userdata` mostly when using libraries written in C. `thread` values are covered in Lesson 22.

---

## 4.7 Type Checking with `type()` and `math.type()`

`type(value)` returns the type name **as a string**:

```lua
print(type(nil))             --> nil
print(type(true))            --> boolean
print(type(10))              --> number
print(type("hi"))            --> string
print(type(print))           --> function
print(type({}))              --> table
print(type(io.stdout))       --> userdata
print(type(coroutine.create(print)))  --> thread
```

Notice that `type(nil)` returns the **string** `"nil"`, so compare with quotes:

```lua
if type(x) == "nil" then ... end   -- works
if type(x) == nil then ... end     -- always false!
```

Since Lua 5.3, numbers have two subtypes, integer and float. `math.type(x)` tells them apart:

```lua
print(math.type(3))      --> integer
print(math.type(3.0))    --> float
print(math.type("3"))    --> nil   (not a number)
```

`math.type` returns `nil` (not an error) for non-numbers, but raises an error if called with no argument. In Lua 5.1 and 5.2, `math.type` does not exist, because all numbers are floats.

---

## 4.8 Type Coercion and Conversion (`tonumber()`, `tostring()`)

Lua **automatically converts** between numbers and strings in some situations:

```lua
print("10" + 5)     --> 15      (string becomes number in arithmetic)
print(10 .. 20)     --> 1020    (numbers become strings in concatenation)
print("3" * "4")    --> 12
```

Automatic conversion fails if the string is not a valid number:

```lua
print(pcall(function() return "abc" + 1 end))
--> false	input:1: attempt to perform arithmetic on a string value (constant 'abc')
```

(`pcall` catches the error and is explained in Lesson 12.)

Other types are never converted automatically. Booleans, tables, and `nil` cause errors in arithmetic and concatenation.

**Explicit conversion** is clearer and safer:

```lua
print(tonumber("42"))        --> 42
print(tonumber("3.5"))       --> 3.5
print(tonumber("0x1F"))      --> 31
print(tonumber("  7  "))     --> 7       (surrounding spaces are fine)
print(tonumber("12abc"))     --> nil     (invalid: returns nil, not an error)
print(tonumber("ff", 16))    --> 255     (second argument is the base)
print(tonumber("1010", 2))   --> 10

print(tostring(123))         --> 123
print(tostring(true))        --> true
print(tostring(nil))         --> nil
print(tostring(1.0))         --> 1.0
```

Always check the result of `tonumber` when the input comes from a user or a file:

```lua
local n = tonumber("hello")
if not n then
  print("That is not a number")
end
```

---

## 4.9 Multiple Assignment and Swapping Values

Lua can assign several variables in one statement. All expressions on the right are evaluated **before** any assignment happens:

```lua
local a, b, c = 1, 2, 3
print(a, b, c)   --> 1	2	3
```

This makes swapping values trivial; no temporary variable needed:

```lua
local x, y = 10, 20
x, y = y, x
print(x, y)      --> 20	10
```

Mismatched counts are adjusted automatically:

```lua
local p, q, r = 1, 2        -- r gets nil (too few values)
print(p, q, r)              --> 1	2	nil

local s, t = 1, 2, 3        -- 3 is evaluated and discarded (too many values)
print(s, t)                 --> 1	2
```

Functions can return several values, which pair naturally with multiple assignment (Lesson 9):

```lua
local first, rest = string.find("hello", "l")
print(first, rest)          --> 3	3
```

---

## 4.10 Constants (`<const>`) and Attributes

Lua 5.4 added **attributes**, written in angle brackets after a local variable name.

**`<const>`** makes a local variable read-only. Trying to assign to it is a **compile-time error**:

```lua
local MAX_PLAYERS <const> = 4
print(MAX_PLAYERS)   --> 4
-- MAX_PLAYERS = 5   -- error: attempt to assign to const variable 'MAX_PLAYERS'
```

Notes:

- `<const>` protects the **variable**, not the contents of a table. A constant table's fields can still change:

```lua
local config <const> = { debug = false }
config.debug = true     -- allowed
print(config.debug)     --> true
-- config = {}          -- NOT allowed
```

- `<const>` works only on `local` variables. There is no const for globals.
- The other attribute, **`<close>`**, runs cleanup code when a variable goes out of scope. It is explained in Lesson 26.

**Version note:** Attributes exist only in Lua 5.4. In 5.1 to 5.3 this syntax is a syntax error. For portable code, use naming convention (`MAX_PLAYERS`) instead.

---

[Previous](./[3]-LuaRocks-And-Packages.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[5]-Numbers-Strings-And-Booleans.md)
