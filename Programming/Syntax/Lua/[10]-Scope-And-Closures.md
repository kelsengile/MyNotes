[Previous](./[9]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[11]-String-Library.md)

*Core Syntax*

# Lesson 10 - Scope, Closures & Upvalues

Scope decides where a name can be seen. Closures let a function remember the variables around it. Together they are one of Lua's most powerful features: they give you private state, factories, and callbacks without any special class syntax.

---

## 10.1 Block Scope and `do ... end`

A **block** is a region of code: a function body, the body of `if`/`while`/`for`/`repeat`, or an explicit `do ... end`. A `local` variable is visible from the line after its declaration to the end of the innermost block that contains it.

```lua
local a = 1
do
  local b = 2
  print(a, b)    --> 1	2
end
print(a, b)      --> 1	nil (b is gone)
```

`do ... end` creates a block just for scoping. It is useful for limiting the lifetime of temporary variables:

```lua
do
  local temp = 10 * 2
  print(temp)    --> 20
  -- temp exists only inside this block
end
```

A `local` can **shadow** (hide) another variable with the same name from an outer scope:

```lua
local x = "outer"
do
  local x = "inner"
  print(x)   --> inner
end
print(x)     --> outer
```

Note that the new local only becomes visible *after* its declaration statement, so `local x = x + 1` reads the outer `x` on the right-hand side:

```lua
local n = 5
do
  local n = n + 1   -- right side uses the outer n
  print(n)          --> 6
end
print(n)            --> 5
```

Lua allows at most 200 local variables active in a function at once, which is plenty unless code is machine-generated.

---

## 10.2 Lexical Scoping

Lua uses **lexical** (also called static) scoping: which variable a name refers to is decided by where the code is **written**, not by where it is called from. Inner functions can see the locals of the functions that enclose them.

```lua
local greeting = "Hello"

local function say(name)
  print(greeting .. ", " .. name)   -- greeting is found in the enclosing scope
end

local function run()
  local greeting = "Goodbye"        -- does NOT affect say()
  say("Ada")
end

run()   --> Hello, Ada
```

When Lua sees a name, it searches outward: first the current block's locals, then enclosing blocks, then enclosing functions, and finally the global environment. The first match wins.

```lua
local level1 = "L1"
local function outer()
  local level2 = "L2"
  local function inner()
    local level3 = "L3"
    print(level1, level2, level3)   --> L1	L2	L3
  end
  inner()
end
outer()
```

---

## 10.3 Global Variables and the `_G` Table

Any name that is not a local is a **global**. All globals live in an ordinary table, accessible as `_G`:

```lua
score = 10
print(_G.score)         --> 10
print(_G["score"])      --> 10
print(_G._G == _G)      --> true (it contains itself)
```

You can read and write globals dynamically through `_G`:

```lua
local varname = "dynamic_value"
_G[varname] = 99
print(dynamic_value)    --> 99
```

The standard library functions are globals too (`print`, `type`, `pairs`), as are the library tables (`string`, `math`, `table`). Counting every global:

```lua
local count = 0
for name in pairs(_G) do count = count + 1 end
print(count > 20)   --> true
```

Why avoid globals?

1. **Speed.** A global access is a table lookup, while a local lives in a register.
2. **Safety.** A typo (`scroe = 5`) silently creates a new global instead of failing.
3. **Collisions.** Any code, including libraries, can overwrite a global.

Lesson 24 shows how to detect accidental globals and explains `_ENV`, the mechanism behind `_G` in Lua 5.2 and later.

---

## 10.4 Closures

A **closure** is a function together with the variables from its enclosing scope that it uses. Because functions are values, an inner function can *outlive* the function that created it, and it keeps access to those variables.

```lua
local function make_greeter(greeting)
  return function(name)
    return greeting .. ", " .. name .. "!"
  end
end

local hello = make_greeter("Hello")
local howdy = make_greeter("Howdy")
print(hello("Ada"))    --> Hello, Ada!
print(howdy("Bob"))    --> Howdy, Bob!
```

After `make_greeter` returned, its parameter `greeting` should have disappeared, but each returned function still carries its own copy of the variable. Two calls produce two independent closures.

Every function value in Lua is technically a closure, even if it uses no outer variables.

---

## 10.5 Upvalues

The variables a closure captures from enclosing functions are called **upvalues**. They are *shared*, not copied: the closure refers to the same variable as the enclosing code, so changes are visible both ways. Several closures created in the same scope share the same upvalues.

```lua
local function make_pair()
  local value = 0
  local function get()      return value end
  local function set(v)     value = v end
  return get, set
end

local get, set = make_pair()
print(get())    --> 0
set(42)
print(get())    --> 42
```

While a closure is alive, its upvalues stay alive too, even after the original scope has ended. This is how state persists without globals.

Error messages mention upvalues, for example `attempt to perform arithmetic on a nil value (upvalue 'total')`, which tells you the problem variable is captured from an outer function. A function can have at most 255 upvalues.

---

## 10.6 Counters, Factories, and Private State

**Counter.** The classic closure example is a counter with state nobody else can touch:

```lua
local function make_counter()
  local count = 0
  return function()
    count = count + 1
    return count
  end
end

local c1 = make_counter()
local c2 = make_counter()
print(c1(), c1(), c1())   --> 1	2	3
print(c2())               --> 1   (independent state)
```

**Factory.** A function that builds specialized functions:

```lua
local function make_adder(n)
  return function(x) return x + n end
end
local add5 = make_adder(5)
print(add5(10))   --> 15
```

**Private state.** Variables captured by closures cannot be reached from outside, so you can expose only the operations you choose:

```lua
local function new_account(initial)
  local balance = initial or 0     -- private: no way to read this directly

  local function deposit(amount)
    balance = balance + amount
    return balance
  end

  local function get_balance()
    return balance
  end

  return { deposit = deposit, get_balance = get_balance }
end

local acct = new_account(100)
acct.deposit(50)
print(acct.get_balance())   --> 150
print(acct.balance)         --> nil (no direct access)
```

Lesson 19 builds on this idea for encapsulation in object-oriented code.

---

## 10.7 Common Closure Pitfalls in Loops

**Loop variables are fresh each iteration.** In Lua, `for` creates a new local for every pass, so closures created inside a `for` loop each capture their own value. This is different from JavaScript's old `var`:

```lua
local funcs = {}
for i = 1, 3 do
  funcs[i] = function() return i end
end
print(funcs[1](), funcs[2](), funcs[3]())   --> 1	2	3
```

**The pitfall: a variable declared outside the loop.** If the closures capture one shared variable declared before the loop, they all see its final value:

```lua
local funcs = {}
local j = 0
while j < 3 do
  j = j + 1
  funcs[j] = function() return j end     -- all capture the same j
end
print(funcs[1](), funcs[2](), funcs[3]())   --> 3	3	3
```

The fix is to copy the value into a local declared **inside** the loop body:

```lua
local funcs2 = {}
local k = 0
while k < 3 do
  k = k + 1
  local captured = k                      -- new variable each pass
  funcs2[k] = function() return captured end
end
print(funcs2[1](), funcs2[2](), funcs2[3]())   --> 1	2	3
```

**Other closure traps:**

- **Memory:** a closure keeps everything it captures alive. A long-lived callback that captures a huge table prevents that table from being collected. Set captured variables to `nil` when finished with them.
- **Unexpected sharing:** two closures that capture the same upvalue affect each other (see 10.5). That is a feature when intended and a bug when not.
- **Modifying the loop variable:** since `for` variables are locals, changing one inside a closure only affects that iteration's copy.

---

[Previous](./[9]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[11]-String-Library.md)
