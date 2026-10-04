[Previous](./[11]-String-Library.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[13]-Tables.md)

*Core Syntax*

# Lesson 12 - Error Handling

Programs fail: files go missing, input is wrong, values are `nil`. This lesson shows how Lua raises, catches, and reports errors, and how to read the messages Lua gives you.

> **Note:** Error messages in the samples start with a position such as `input:3:`. That is the chunk name used by the online demo. When you run a file, you will see the file name instead (for example `test.lua:3:`), and the line numbers will match your own file.

---

## 12.1 Errors in Lua

There are three broad kinds of problems:

**1. Syntax errors** are found when the code is compiled, before anything runs:

```text
lua: test.lua:3: '=' expected near 'print'
```

**2. Runtime errors** happen while the program runs: calling `nil`, adding a number to a string, indexing a `nil` value, and so on.

```lua
local t = nil
print(t.name)   -- error: attempt to index a nil value (local 't')
```

**3. Logic errors** are bugs where the program runs fine but gives the wrong result. Lua cannot detect those; tests and debugging help (Lessons 40 and 42).

When an error is **not** caught, Lua stops the program, prints the message with a stack traceback to standard error, and exits with a failure status:

```text
lua: test.lua:2: attempt to index a nil value (local 't')
stack traceback:
        test.lua:2: in main chunk
        [C]: in ?
```

An error can carry **any Lua value**, not only a string; strings are by far the most common.

---

## 12.2 `error()` and Error Levels

`error(message [, level])` raises an error, stopping the current function and unwinding the stack until something catches it:

```lua
local function divide(a, b)
  if b == 0 then
    error("division by zero")
  end
  return a / b
end

print(pcall(divide, 1, 0))
--> false	input:3: division by zero
```

For string messages, `error` prepends the **position** (file and line) where the error occurred. The optional `level` controls which position is used:

| Level | Position added |
|---|---|
| `1` (default) | The function that called `error` |
| `2` | The function that **called** that function (blames the caller) |
| `0` | No position information |

Level 2 is useful in library functions to blame the caller for bad arguments, which points the user to their own mistake rather than your internals:

```lua
local function set_age(age)
  if type(age) ~= "number" then
    error("age must be a number", 2)   -- report the caller's line
  end
  return age
end

local ok, err = pcall(function() set_age("old") end)
print(err)   --> input:8: age must be a number
```

Messages should be short, lowercase, and say what went wrong. Include the offending value when helpful:

```lua
error(string.format("invalid port %q (expected 1-65535)", tostring(port)))
```

---

## 12.3 `assert()`

`assert(v [, message, ...])` checks that `v` is truthy. If it is `nil` or `false`, it raises an error with `message` (default: `"assertion failed!"`). If `v` is truthy, `assert` returns **all its arguments**, which makes it very handy for wrapping functions that report failure with `nil, err`:

```lua
local x = assert(tonumber("42"))
print(x)   --> 42

print(pcall(assert, false))                 --> false	assertion failed!
print(pcall(assert, nil, "value required")) --> false	value required

-- Idiom: fail loudly if a file cannot be opened
local f = assert(io.open("settings.txt", "r"))   -- error message comes from io.open
```

Notes:

- Unlike `error`, `assert` does **not** add position information to the message.
- The message argument is **always evaluated**, even when the assertion passes, so avoid expensive string building there:

```lua
-- Wasteful: builds the string every time
assert(x > 0, "x was " .. expensive_description(x))

-- Better: only do the work on failure
if not (x > 0) then error("x was " .. expensive_description(x)) end
```

Use `assert` for conditions that should never be false (programmer errors) and for unwrapping results that must succeed.

---

## 12.4 Protected Calls with `pcall()`

`pcall(f, ...)` ("protected call") runs `f` with the given arguments **without letting an error escape**. It returns:

- `true` followed by the function's results if it succeeds.
- `false` followed by the error value if it fails.

```lua
local ok, result = pcall(function(a, b) return a + b end, 3, 4)
print(ok, result)   --> true	7

local ok2, err = pcall(function() error("oops") end)
print(ok2, err)     --> false	input:1: oops

local ok3, err3 = pcall(function() local t; return t.x end)
print(ok3, err3)    --> false	input:1: attempt to index a nil value (local 't')
```

A typical pattern:

```lua
local ok, err = pcall(risky_function)
if not ok then
  print("Something went wrong: " .. tostring(err))
end
```

Important points:

- `pcall` catches errors raised by `error`, `assert`, and runtime errors alike.
- By the time `pcall` returns, the stack has already unwound, so the traceback is lost. Use `xpcall` (12.5) if you need one.
- Don't use `pcall` to hide bugs. Catch errors where you can do something sensible: retry, show a message, clean up, or convert to `nil, err`.
- To re-raise an error after handling it: `error(err, 0)` (level 0 avoids adding another position prefix).

---

## 12.5 `xpcall()` and Message Handlers

`xpcall(f, handler, ...)` works like `pcall`, but calls `handler(err)` **at the point of the error** (before the stack unwinds), letting you add information such as a traceback. Whatever the handler returns becomes the error value:

```lua
local function handler(err)
  return "handled: " .. tostring(err)
end

local ok, msg = xpcall(function() error("boom") end, handler)
print(ok, msg)   --> false	handled: input:5: boom
```

To log a traceback, pass `debug.traceback` as the handler:

```lua
local ok, trace = xpcall(function()
  local t = nil
  return t.field
end, debug.traceback)

print(ok)      --> false
print(trace)   -- the message followed by "stack traceback:" and the call chain
```

**Version note:** In Lua 5.1, `xpcall` did not accept extra arguments to pass to `f`. Wrap the call in a function instead: `xpcall(function() return f(a, b) end, handler)`. That form works in every version.

If the handler itself raises an error, Lua reports a special status; keep handlers simple.

---

## 12.6 Tracebacks (`debug.traceback`)

`debug.traceback([message [, level]])` returns a string containing the message followed by a **stack traceback**: the chain of function calls that led to the current point.

```lua
local function inner() print(debug.traceback("checkpoint")) end
local function outer() inner() end
outer()
```

Example output:

```text
checkpoint
stack traceback:
        [C]: in function 'debug.traceback'
        test.lua:1: in upvalue 'inner'
        test.lua:2: in local 'outer'
        test.lua:3: in main chunk
        [C]: in ?
```

Read it from top to bottom: the top is where you are, and each line below is the caller. Each line shows `file:line: in <function description>`.

Tracebacks are most useful inside an `xpcall` handler (12.5), where they describe where the error occurred rather than where it was caught. The standalone `lua` interpreter uses this internally to print its error output. Lesson 42 covers reading traces in more depth.

---

## 12.7 Throwing Tables as Custom Error Objects

An error value can be a **table**, which allows you to carry a code, a message, and extra data. Callers can then react to the *kind* of error instead of parsing strings.

```lua
local function fail(code, message, extra)
  error({ code = code, message = message, extra = extra })
end

local function load_user(id)
  if id < 0 then
    fail("INVALID_ID", "id must be positive", { id = id })
  end
  return { id = id }
end

local ok, e = pcall(load_user, -1)
if not ok then
  if type(e) == "table" and e.code == "INVALID_ID" then
    print("Bad id:", e.extra.id, e.message)   --> Bad id:	-1	id must be positive
  else
    print("Unexpected error:", e)
  end
end
```

Facts to keep in mind:

- When error values are tables, **no position information is added**; include any you need yourself.
- If an uncaught table error reaches the top level, the standalone interpreter prints `lua: (error object is a table value)`, unless the table has a `__tostring` metamethod. Give your error objects a `__tostring`:

```lua
local MyError = {}
MyError.__index = MyError
MyError.__tostring = function(e) return "MyError: " .. e.message end

local function throw(msg)
  error(setmetatable({ message = msg }, MyError))
end

local ok, e = pcall(throw, "disk full")
print(tostring(e))   --> MyError: disk full
```

- Always check `type(e)` before indexing the error value, because errors raised by Lua itself are strings.

---

## 12.8 Return `nil, err` Convention

Raising an error is right for **bugs** and truly exceptional situations. For *expected* failures (a missing file, bad user input, a network timeout), the Lua convention is to **return `nil` and an error message**, and let the caller decide what to do:

```lua
local function read_number(str)
  local n = tonumber(str)
  if not n then
    return nil, "not a number: " .. tostring(str)
  end
  return n
end

local n, err = read_number("abc")
if not n then
  print("Error:", err)   --> Error:	not a number: abc
end
```

The standard library follows this pattern: `io.open` returns `nil, message, errno` when a file cannot be opened, and `tonumber` returns `nil`.

```lua
local f, err = io.open("/no/such/file", "r")
if not f then
  print("Could not open:", err)   --> Could not open:	/no/such/file: No such file or directory
end
```

The two styles combine: callers who want an exception can wrap the call in `assert`, as in `local f = assert(io.open(name))`, which turns `nil, err` back into a raised error.

| Situation | Use |
|---|---|
| Programmer mistake (wrong argument type) | `error(...)` |
| Impossible state | `assert(...)` |
| Expected, recoverable failure | `return nil, err` |
| Run risky code and keep going | `pcall` / `xpcall` |

---

## 12.9 Reading Common Error Messages

Lua's messages are descriptive once you know the patterns. They follow the format `file:line: message`.

| Message | Meaning and usual cause |
|---|---|
| `attempt to index a nil value (local 'x')` | You wrote `x.field` or `x[i]` but `x` is `nil`: a misspelled name, missing return value, or missing key. |
| `attempt to index a nil value (field 'config')` | A table field you expected to be a table is `nil` (`t.config.name`). |
| `attempt to call a nil value (global 'foo')` | You called a function that does not exist: misspelled name, not defined yet, or module not loaded. |
| `attempt to call a nil value (method 'bar')` | `obj:bar()` where `bar` is missing from the object or its class. |
| `attempt to call a table value` | You called something that is a table (maybe forgot `:` or a field name). |
| `attempt to perform arithmetic on a nil value (local 'n')` | Math with a missing value. |
| `attempt to perform arithmetic on a string value` | A string that cannot be converted to a number. |
| `attempt to concatenate a nil value (local 'name')` | `..` with `nil` (or a boolean/table). Use `tostring`. |
| `attempt to compare number with nil` | `<`, `>` between mismatched types, often a missing key (`if t.count > 3`). |
| `attempt to compare two table values` | Tables have no ordering unless `__lt` is defined. |
| `bad argument #1 to 'insert' (table expected, got nil)` | A library function received the wrong argument. Count arguments from 1; with method calls like `s:rep()`, the object is argument #1. |
| `number has no integer representation` | A float like `3.5` used where an integer is required (the `%d` format, bitwise operators, and so on). |
| `'for' initial value must be a number` | Non-number given to a numeric `for`. |
| `stack overflow` | Infinite or too-deep recursion. |
| `unexpected symbol near '...'` / `'end' expected` | Syntax errors: typos, missing `end`, `then`, or `do`. |
| `module 'x' not found` | `require` could not find the file; check `package.path` (Lesson 3.6). |

**How to read the parenthesis at the end.** Lua tells you *what kind of name* is involved: `local`, `global`, `field`, `upvalue`, `method`, or `constant`. That points straight to the variable to inspect.

**Debugging steps:**

1. Read the **line number** and open that line.
2. Identify the **value that is `nil`** (or wrong type) using the hint in parentheses.
3. Work backward: where should this value have been assigned?
4. Print the value (`print(x, type(x))`) just before the failing line.

---

[Previous](./[11]-String-Library.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[13]-Tables.md)
