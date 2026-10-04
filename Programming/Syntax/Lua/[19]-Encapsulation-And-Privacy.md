[Previous](./[18]-Inheritance-And-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[20]-Multiple-Inheritance-And-Mixins.md)

*Object-Oriented Programming*

# Lesson 19 - Encapsulation & Privacy

Lua trusts the programmer: any code can read or change any field of a table. There is no `private` keyword. But when you want to protect an object's internals, Lua gives you several techniques, from simple naming rules to truly inaccessible state. This lesson compares them and shows when each one is worth its cost.

---

## 19.1 What is Encapsulation in Lua?

**Encapsulation** means bundling an object's data with the functions that work on it, and hiding the details that outside code should not touch. The outside world uses a small, stable **public interface**; the **internals** can change freely without breaking anyone.

Without any protection, nothing stops this:

```lua
local Account = {}
Account.__index = Account

function Account.new(balance)
  return setmetatable({ balance = balance }, Account)
end

function Account:deposit(amount)
  assert(amount > 0, "deposit must be positive")
  self.balance = self.balance + amount
end

local acct = Account.new(100)
acct.balance = -5000          -- bypasses all validation!
print(acct.balance)           --> -5000
```

Lua offers four levels of protection, from lightest to strongest:

| Technique | How it works | Truly private? | Cost |
|---|---|---|---|
| Naming convention (19.2) | Prefix internal names with `_` | No (honor system) | None |
| Closures (19.3) | State lives in upvalues | Yes | Extra memory per object |
| Proxy tables (19.4) | Callers see an empty stand-in | Yes | Slower access |
| Read-only objects (19.5) | Writes are rejected | Protects against changes | Slower access |

Most Lua code uses the first technique and nothing more. Reach for the stronger ones when you are writing a library that others will use, handling untrusted data, or want to catch accidental misuse early.

---

## 19.2 Naming Conventions (`_private`)

The simplest approach, and the most common in practice, is a **convention**: names that start with an underscore are internal, and users of the class agree not to touch them.

```lua
local Counter = {}
Counter.__index = Counter

function Counter.new()
  return setmetatable({ _count = 0 }, Counter)    -- _count is "private"
end

function Counter:_validate(step)                  -- private method
  assert(type(step) == "number", "step must be a number")
end

function Counter:increment(step)                  -- public method
  step = step or 1
  self:_validate(step)
  self._count = self._count + step
end

function Counter:value()                          -- public accessor
  return self._count
end

local c = Counter.new()
c:increment()
c:increment(5)
print(c:value())    --> 6
```

Why it works: anyone reading `c._count` immediately sees that they are reaching into internals, and it is their responsibility if the class changes.

Advantages:

- No runtime cost and no extra code.
- Easy to debug: you can still inspect `_count` with `print` or a debugger.
- Works naturally with inheritance and metatables.

Disadvantages:

- Nothing is **enforced**. Code can read or overwrite `_count`.

Tools can help. The Lua Language Server (Lesson 41) understands annotations such as `---@private` and warns when code outside the class uses a private member:

```lua
---@class Counter
---@field private _count number
```

Some projects use two underscores (`__name`) or a trailing underscore for "very private". Pick one style, document it, and be consistent. Note that names beginning with a double underscore overlap with metamethod names (`__index`, `__add`), so avoid inventing your own `__` names to prevent confusion.

---

## 19.3 Privacy with Closures

A closure keeps its **upvalues** alive and reachable only through the functions that captured them (Lesson 10). If an object's state is stored in upvalues instead of in the object table, nothing outside can reach it.

```lua
local function new_account(owner, initial)
  -- private state: only visible inside new_account
  local balance = initial or 0
  local history = {}

  -- private function
  local function record(kind, amount)
    history[#history + 1] = kind .. " " .. amount
  end

  -- public interface: the only way to touch the state
  local self = {}

  function self.deposit(amount)
    assert(amount > 0, "deposit must be positive")
    balance = balance + amount
    record("deposit", amount)
  end

  function self.withdraw(amount)
    if amount > balance then
      return nil, "insufficient funds"
    end
    balance = balance - amount
    record("withdraw", amount)
    return balance
  end

  function self.get_balance() return balance end
  function self.get_owner() return owner end
  function self.history_count() return #history end

  return self
end

local acct = new_account("Ada", 100)
acct.deposit(50)
print(acct.withdraw(500))        --> nil	insufficient funds
print(acct.withdraw(30))         --> 120
print(acct.get_balance())        --> 120
print(acct.balance)              --> nil   (no such field; the real one is private)
print(acct.history_count())      --> 2
```

There is no field named `balance` on `acct`; the variable only exists as an upvalue shared by the functions inside `new_account`.

Notes about this style:

- The functions use **dot** calls (`acct.deposit(50)`) because they already know the state they belong to, so they do not need a `self` argument.
- Every object creates its own set of closures, so each instance costs more memory than a metatable-based object, where methods are shared through the class.
- Inheritance is harder, because methods are not found through a class table.

**Sharing methods while keeping state private.** If you want ordinary metatable classes (shared methods, colon calls, inheritance) with private data, store the data in a table that only your module can see, keyed by the object:

```lua
local Account = {}
Account.__index = Account

local private = setmetatable({}, { __mode = "k" })   -- hidden state, keyed by object

function Account.new(owner, balance)
  local obj = setmetatable({}, Account)              -- the public object stays empty
  private[obj] = { owner = owner, balance = balance or 0 }
  return obj
end

function Account:deposit(amount)
  local data = private[self]
  data.balance = data.balance + amount
end

function Account:get_balance()
  return private[self].balance
end

local a = Account.new("Ada", 100)
a:deposit(25)
print(a:get_balance())   --> 125
print(a.balance)         --> nil
```

`private` is a local variable of the module file, so no outside code can reach it. The `"k"` setting makes its keys **weak**, so an account's hidden data is freed automatically when the account itself is no longer used (weak tables are covered in Lesson 26).

---

## 19.4 Privacy with Proxy Tables

A **proxy** is an empty table that stands in for the real object. All reads and writes go through metamethods (`__index` and `__newindex`, Lesson 16), which decide what to allow. The real data is hidden in an upvalue.

```lua
local function new_user(name, password)
  local data = { name = name, password = password, logins = 0 }
  local readable = { name = true, logins = true }     -- fields callers may read

  local methods = {}
  function methods.login(self, attempt)
    if attempt == data.password then
      data.logins = data.logins + 1
      return true
    end
    return false
  end

  return setmetatable({}, {
    __index = function(_, key)
      if methods[key] then return methods[key] end    -- public methods
      if readable[key] then return data[key] end      -- allowed fields only
      return nil                                      -- everything else is hidden
    end,
    __newindex = function(_, key)
      error("cannot set field '" .. tostring(key) .. "'", 2)
    end,
    __metatable = "locked",                           -- block getmetatable/setmetatable
  })
end

local u = new_user("ada", "s3cret")
print(u.name)                  --> ada
print(u:login("wrong"))        --> false
print(u:login("s3cret"))       --> true
print(u.logins)                --> 1
print(u.password)              --> nil   (hidden)
print(pcall(function() u.name = "eve" end))
--> false	input:...: cannot set field 'name'
print(getmetatable(u))         --> locked
```

Because the proxy is always empty, `__newindex` fires for **every** assignment, so callers cannot add or change anything. And since `__metatable` is set, they cannot swap in a different metatable to get around the checks.

Trade-offs:

- Strongest protection of the four techniques, but every access runs a function, so it is noticeably slower.
- `pairs(u)` and `#u` see an empty table unless you add `__pairs` and `__len` (see the read-only example in Lesson 16.10).
- Debugging is harder because the real data is not visible when you print the object.

Use proxies when the extra safety matters (security-sensitive data, public library objects), not for every small class.

---

## 19.5 Read-Only Objects

Sometimes the goal is not hiding data but **preventing changes**: constants, configuration, or immutable value objects.

**A read-only wrapper** keeps the data in a hidden table and rejects writes (the pattern from Lesson 16.10). To protect nested tables too, wrap them as they are read:

```lua
local function readonly(t)
  local cache = {}                                  -- one wrapper per nested table

  local function wrap(value)
    if type(value) ~= "table" then return value end
    cache[value] = cache[value] or readonly(value)
    return cache[value]
  end

  return setmetatable({}, {
    __index = function(_, key) return wrap(t[key]) end,
    __newindex = function(_, key)
      error("attempt to modify read-only field '" .. tostring(key) .. "'", 2)
    end,
    __len = function() return #t end,
    __pairs = function()
      local key
      return function()
        local value
        key, value = next(t, key)
        if key ~= nil then return key, wrap(value) end
      end
    end,
    __metatable = false,
  })
end

local config = readonly({
  name = "app",
  window = { width = 800, height = 600 },
  tags = { "a", "b" },
})

print(config.name)             --> app
print(config.window.width)     --> 800
print(#config.tags)            --> 2
print(pcall(function() config.name = "x" end))
--> false	input:...: attempt to modify read-only field 'name'
print(pcall(function() config.window.width = 1 end))
--> false	input:...: attempt to modify read-only field 'width'
```

**Immutable objects.** An immutable class never changes an instance; "modifying" methods return a **new** instance instead. This is safe to share freely, because no one can alter it:

```lua
local Point = {}
Point.__index = Point

function Point.new(x, y)
  local data = { x = x, y = y }
  return setmetatable({}, {
    __index = function(_, key)
      if data[key] ~= nil then return data[key] end
      return Point[key]                              -- methods from the class
    end,
    __newindex = function() error("Point is immutable", 2) end,
    __tostring = function() return "(" .. data.x .. ", " .. data.y .. ")" end,
    __metatable = false,
  })
end

function Point:with_x(new_x) return Point.new(new_x, self.y) end
function Point:translate(dx, dy) return Point.new(self.x + dx, self.y + dy) end

local p = Point.new(1, 2)
local q = p:translate(10, 0)
print(p, q)                                   --> (1, 2)	(11, 2)
print(pcall(function() p.x = 99 end))         --> false	input:...: Point is immutable
```

**Constants for plain variables.** In Lua 5.4, `local NAME <const> = value` stops a *variable* from being reassigned (Lesson 4.10), but it does not protect the contents of a table. For that you need a read-only wrapper like the ones above.

**Version note:** Luau (Roblox) provides `table.freeze` to make a table read-only. Standard Lua has no equivalent, so proxies are the portable solution.

---

## 19.6 Interfaces via Duck Typing

An **interface** is a promise about which methods an object provides. Lua has no `interface` keyword, and thanks to duck typing (Lesson 18.6) it does not need one: any object with the right methods fits. What you can add is a **check** that fails early with a clear message when an object does not keep the promise.

Describe the interface as a list of required method names:

```lua
local Drawable = { "draw", "bounds" }          -- required methods
local Updatable = { "update" }

local function implements(obj, interface)
  for _, name in ipairs(interface) do
    if type(obj[name]) ~= "function" then
      return false, name                       -- also report what is missing
    end
  end
  return true
end

local function require_interface(obj, interface, label)
  local ok, missing = implements(obj, interface)
  if not ok then
    error((label or "object") .. " is missing method '" .. missing .. "'", 2)
  end
  return obj
end
```

Two unrelated classes can satisfy the same interface:

```lua
local Circle = {}
Circle.__index = Circle
function Circle.new(r) return setmetatable({ r = r }, Circle) end
function Circle:draw() return "circle r=" .. self.r end
function Circle:bounds() return self.r * 2, self.r * 2 end

local Label = {}
Label.__index = Label
function Label.new(text) return setmetatable({ text = text }, Label) end
function Label:draw() return "label '" .. self.text .. "'" end
function Label:bounds() return #self.text * 8, 12 end

local Broken = { draw = function() end }       -- has draw but no bounds

local scene = {}
local function add(obj)
  scene[#scene + 1] = require_interface(obj, Drawable, "scene item")
end

add(Circle.new(5))
add(Label.new("hi"))
print(pcall(add, Broken))
--> false	input:...: scene item is missing method 'bounds'

for _, item in ipairs(scene) do
  print(item:draw())
end
--> circle r=5
--> label 'hi'
```

**Abstract methods.** A base class can declare a method that every subclass must provide, and raise a clear error if one forgets:

```lua
local Shape = {}
Shape.__index = Shape

function Shape:area()
  error("subclass must implement area()", 2)
end

function Shape:describe()
  return "area = " .. self:area()              -- template method: calls the abstract one
end

local Square = setmetatable({}, { __index = Shape })
Square.__index = Square
function Square.new(s) return setmetatable({ s = s }, Square) end
function Square:area() return self.s ^ 2 end

local Blob = setmetatable({}, { __index = Shape })
Blob.__index = Blob

print(Square.new(3):describe())                          --> area = 9.0
print(pcall(Shape.describe, setmetatable({}, Blob)))
--> false	input:...: subclass must implement area()
```

Tips for using interface checks:

- Check **once**, at the boundary where an object enters your system (when adding to a list, registering a handler, and so on), not on every call.
- Keep interfaces small. Two or three methods are easier to satisfy than ten.
- Document the interface in a comment or an annotation (Lesson 41) so that people writing new classes know what to provide.

---

[Previous](./[18]-Inheritance-And-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[20]-Multiple-Inheritance-And-Mixins.md)
