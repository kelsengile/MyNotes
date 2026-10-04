[Previous](./[8]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[10]-Scope-And-Closures.md)

*Core Syntax*

# Lesson 9 - Functions

Functions package code so you can reuse it. In Lua, functions are ordinary values: you can store them in variables, pass them around, and return them. This lesson covers everything from basic definitions to variadic arguments and tail calls.

---

## 9.1 Defining and Calling Functions

Define a function with the `function` keyword:

```lua
function greet(name)
  print("Hello, " .. name .. "!")
end

greet("Ada")   --> Hello, Ada!
```

This is just shorthand for assigning a function value to a variable:

```lua
greet = function(name)
  print("Hello, " .. name .. "!")
end
```

**Prefer `local` functions.** A plain `function name()` creates a *global*. Use `local function` for helpers:

```lua
local function square(x)
  return x * x
end
print(square(5))   --> 25
```

`local function f` is also needed for **recursion** (a function calling itself), because it makes `f` visible inside its own body. Writing `local f = function() ... f() ... end` would not work, because the `f` inside refers to a different (global) `f`.

**Calling shortcuts.** If a function takes a single string literal or table constructor, the parentheses may be omitted:

```lua
print "hello"             -- same as print("hello")
local function show(t) print(t[1], t[2]) end
show { "a", "b" }         -- same as show({ "a", "b" })
```

Functions without a `return` statement return nothing. Calling a function that expects arguments with too few simply gives the missing ones the value `nil`:

```lua
local function add(a, b) return a + b end
print(pcall(add, 1))   --> false	input:1: attempt to perform arithmetic on a nil value (local 'b')
```

---

## 9.2 Parameters, Arguments & Return Values

**Parameters** are the names in the definition; **arguments** are the values passed in a call. Lua is flexible about counts:

- Extra arguments are silently **discarded**.
- Missing arguments become **`nil`**.

```lua
local function show(a, b)
  print(a, b)
end

show(1, 2)      --> 1	2
show(1)         --> 1	nil
show(1, 2, 3)   --> 1	2   (3 is ignored)
```

Use `return` to send values back. Execution of the function ends at `return`, and `return` must be the last statement in its block.

```lua
local function max2(a, b)
  if a > b then return a end
  return b
end
print(max2(3, 9))   --> 9
```

Arguments are passed **by value**, but for tables, functions, and other objects, the "value" is a reference to the same object. Changing a table's contents inside a function is visible to the caller; reassigning the parameter is not:

```lua
local function modify(t)
  t.changed = true     -- affects the caller's table
  t = {}               -- only changes the local parameter
end
local data = {}
modify(data)
print(data.changed)    --> true
```

---

## 9.3 Multiple Return Values

A function can return several values:

```lua
local function min_max(list)
  local lo, hi = list[1], list[1]
  for _, v in ipairs(list) do
    if v < lo then lo = v end
    if v > hi then hi = v end
  end
  return lo, hi
end

local smallest, largest = min_max({ 4, 9, 2, 7 })
print(smallest, largest)   --> 2	9
```

How the results are adjusted:

```lua
local function three() return 1, 2, 3 end

local a = three()          -- a = 1 (extras dropped)
local x, y, z, w = three() -- w = nil (missing become nil)
print(three())             --> 1	2	3  (all kept when last in an argument list)
print(three(), 10)         --> 1	10     (not last, so truncated to one value)
print((three()))           --> 1          (parentheses truncate to one value)

local t = { three(), three() }
print(#t)                  --> 4  (first call truncated to 1; second expands to 3)
```

The rule: a function call that is the **last expression** in a list (arguments, table constructor, return, assignment) expands to all its values. In any other position it is cut to exactly one value.

Many standard functions use multiple returns, such as `string.find` (start and end positions) and `pcall` (success flag plus results).

---

## 9.4 Variadic Functions (`...`, `select`, `table.pack`, `table.unpack`)

A function can accept any number of arguments by ending its parameter list with `...`:

```lua
local function sum(...)
  local total = 0
  for _, v in ipairs({ ... }) do
    total = total + v
  end
  return total
end
print(sum(1, 2, 3, 4))   --> 10
```

Ways to work with `...`:

**`{ ... }`** packs the arguments into a table, but a `nil` among them creates a hole, which makes `#` unreliable.

**`select("#", ...)`** returns the exact argument count (including `nil`s). **`select(n, ...)`** returns all arguments from position `n` onward:

```lua
local function info(...)
  print("count:", select("#", ...))
  print("second onward:", select(2, ...))
end
info("a", nil, "c")
--> count:	3
--> second onward:	nil	c
```

**`table.pack(...)`** returns a table with all arguments and a field `n` holding the count, which is safe even with `nil`s:

```lua
local function count_args(...)
  local args = table.pack(...)
  return args.n
end
print(count_args(1, nil, nil))   --> 3
```

**`table.unpack(t [, i [, j]])`** is the reverse: it turns a table's elements back into separate values:

```lua
local nums = { 10, 20, 30 }
print(table.unpack(nums))           --> 10	20	30
print(math.max(table.unpack(nums))) --> 30
print(table.unpack(nums, 2, 3))     --> 20	30
```

Forward all arguments to another function with `...`:

```lua
local function logged_print(...)
  io.write("[log] ")
  print(...)
end
logged_print("a", "b")   --> [log] a	b
```

**Version note:** In Lua 5.1 and LuaJIT, `table.unpack` is the global function `unpack`, and `table.pack` does not exist (use `{ n = select("#", ...), ... }` instead). For portable code: `local unpack = table.unpack or unpack`.

---

## 9.5 Default Arguments (via `or`)

Lua has no syntax for default parameter values. The usual trick is `or`:

```lua
local function greet(name, greeting)
  greeting = greeting or "Hello"
  name = name or "stranger"
  print(greeting .. ", " .. name .. "!")
end

greet()                 --> Hello, stranger!
greet("Ada")            --> Hello, Ada!
greet("Ada", "Welcome") --> Welcome, Ada!
```

**Pitfall with `false`:** `x = x or default` also replaces an explicit `false`, since `false` is falsy:

```lua
local function set_sound(enabled)
  enabled = enabled or true     -- BUG: passing false still gives true
  return enabled
end
print(set_sound(false))         --> true (wrong!)
```

For boolean parameters, test for `nil` explicitly:

```lua
local function set_sound2(enabled)
  if enabled == nil then enabled = true end
  return enabled
end
print(set_sound2(false))        --> false
```

---

## 9.6 Named Arguments via Tables

When a function has many parameters, positional arguments become hard to read (`create(10, 20, true, nil, "x")`). Lua's alternative is to pass **one table** and use its fields as named arguments. Combined with the call shortcut (no parentheses needed for a single table argument), calls read nicely:

```lua
local function create_window(opts)
  local width  = opts.width  or 800
  local height = opts.height or 600
  local title  = opts.title  or "Untitled"
  print(title, width .. "x" .. height)
end

create_window { title = "Editor", width = 1024 }
--> Editor	1024x600

create_window { }
--> Untitled	800x600
```

Advantages: order does not matter, optional values are easy, and you can add new options later without breaking existing calls. For required options, validate them:

```lua
local function connect(opts)
  assert(type(opts) == "table", "options table expected")
  assert(opts.host, "host is required")
  print("connecting to " .. opts.host .. ":" .. (opts.port or 80))
end
connect { host = "example.com" }   --> connecting to example.com:80
```

---

## 9.7 Functions as First-Class Values

"First-class" means functions are treated like any other value. You can:

- Store them in variables and table fields.
- Pass them as arguments.
- Return them from other functions.

```lua
local function add(a, b) return a + b end
local op = add                    -- store in another variable
print(op(2, 3))                   --> 5

local math_ops = {
  add = function(a, b) return a + b end,
  mul = function(a, b) return a * b end,
}
print(math_ops.mul(4, 5))         --> 20

local function apply(f, x, y)     -- pass a function in
  return f(x, y)
end
print(apply(math_ops.add, 10, 5)) --> 15

local function make_multiplier(n) -- return a function
  return function(x) return x * n end
end
local triple = make_multiplier(3)
print(triple(7))                  --> 21
```

Functions that take or return other functions are called **higher-order functions**; they are the heart of functional-style Lua (Lesson 25). Functions can also be used as table keys, and `type(f)` returns `"function"`.

---

## 9.8 Anonymous Functions

A function without a name is called an **anonymous function** (or lambda). It is created inline with `function(...) ... end`:

```lua
local numbers = { 5, 2, 8, 1 }

table.sort(numbers, function(a, b) return a > b end)
print(table.concat(numbers, ", "))   --> 8, 5, 2, 1
```

Anonymous functions are used for callbacks, sort comparators, event handlers, and immediate one-off calls:

```lua
local result = (function(a, b) return a * b end)(6, 7)
print(result)   --> 42
```

Note: the named-function syntax is just sugar for assigning an anonymous function, so there is no deep difference. One side effect: error messages and tracebacks label anonymous functions with locations such as `in function <input:3>`, which can make debugging a bit harder. Give a function a name if it is reused or complicated.

---

## 9.9 Recursion and Proper Tail Calls

A **recursive** function calls itself. Use `local function` so the name is visible in the body:

```lua
local function factorial(n)
  if n <= 1 then return 1 end
  return n * factorial(n - 1)
end
print(factorial(5))   --> 120
```

Every call uses stack space. Recursing too deeply raises a "stack overflow" error. The exact limit depends on the Lua version and on how much each call uses, but it is typically in the tens or hundreds of thousands of nested calls.

**Proper tail calls.** If a function's last action is to `return` a call to another function, with nothing else done to the result, Lua performs a **tail call**: it reuses the current stack frame, so the stack does not grow. This allows loops written as recursion that can run forever:

```lua
local function count_down(n)
  if n == 0 then return "done" end
  return count_down(n - 1)     -- tail call: nothing happens after it returns
end
print(count_down(1000000))     --> done (no stack overflow)
```

Compare with `return n * factorial(n - 1)` above: the multiplication happens *after* the call returns, so it is **not** a tail call. To make factorial tail-recursive, pass an accumulator:

```lua
local function fact(n, acc)
  acc = acc or 1
  if n <= 1 then return acc end
  return fact(n - 1, acc * n)  -- tail call
end
print(fact(20))   --> 2432902008176640000
```

Writing `return (f(x))` (with extra parentheses) is **not** a tail call, since the result is truncated to one value.

---

## 9.10 Method Syntax (`:` vs `.`)

Tables can hold functions, and Lua offers special syntax for calling them as **methods**. The colon `:` passes the table itself as a hidden first argument called `self`:

```lua
local account = { balance = 100 }

function account.deposit(self, amount)   -- explicit self
  self.balance = self.balance + amount
end

function account:withdraw(amount)        -- colon definition adds self automatically
  self.balance = self.balance - amount
end

account.deposit(account, 50)   -- dot call: pass self manually
account:withdraw(30)           -- colon call: self passed automatically
print(account.balance)         --> 120
```

The equivalences:

| Colon form | Dot form |
|---|---|
| `function t:name(a) ... end` | `function t.name(self, a) ... end` |
| `t:name(x)` | `t.name(t, x)` |

Mixing them up is a very common mistake: calling `account.withdraw(30)` makes `30` the `self` argument and `amount` becomes `nil`. Rule of thumb: define with `:` when the function needs the object, and call with `:` too.

Strings work the same way thanks to their metatable: `("hi"):upper()` equals `string.upper("hi")` (Lesson 11). Method syntax is the foundation of Lua's object-oriented programming, covered in Lesson 17.

---

[Previous](./[8]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[10]-Scope-And-Closures.md)
