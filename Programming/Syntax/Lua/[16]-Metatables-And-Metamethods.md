[Previous](./[15]-Common-Data-Structures.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[17]-Objects-And-Classes.md)

*Data Structures*

# Lesson 16 - Metatables & Metamethods

Metatables let you change how tables behave: what happens when you read a missing key, add two tables with `+`, call a table like a function, or print one. They are the mechanism behind Lua's object system, operator overloading, default values, and read-only tables.

---

## 16.1 What is a Metatable?

Every table can have a second table attached to it, called its **metatable**. The metatable contains special fields, called **metamethods**, whose names start with two underscores (`__index`, `__add`, `__tostring`, ...). When Lua performs an operation on a table that it does not know how to handle, it looks for the matching metamethod in the metatable.

```lua
local v = { x = 1 }
local mt = {
  __tostring = function(self) return "Vec(" .. self.x .. ")" end,
}
setmetatable(v, mt)
print(v)       --> Vec(1)   (print calls tostring, which finds __tostring)
```

Key points:

- A **metatable is just a regular table**; it is special only because it is attached to another table.
- Each table has **at most one** metatable, but one metatable can be shared by many tables.
- Tables and userdata have individual metatables. All values of the other types (numbers, strings, functions, ...) share one metatable per type. Only the `string` type has one by default (which is how `("x"):upper()` works).
- Metamethods are looked up with **raw** access, so they are not themselves inherited through `__index`.

---

## 16.2 `setmetatable()` and `getmetatable()`

**`setmetatable(t, mt)`** attaches `mt` as the metatable of `t` (or removes it when `mt` is `nil`) and **returns `t`**, which allows compact one-line construction:

```lua
local mt = {}
local t = setmetatable({}, mt)
print(getmetatable(t) == mt)     --> true
```

**`getmetatable(obj)`** returns the metatable, or `nil` if there is none:

```lua
print(getmetatable({}))          --> nil
print(getmetatable("abc").__index == string)   --> true
```

If the metatable has a `__metatable` field, `getmetatable` returns **that value** instead and `setmetatable` refuses to change it (16.9).

From Lua code you can only set metatables on tables. To set metatables for other types you need the C API or the `debug` library (`debug.setmetatable`).

---

## 16.3 `__index` (Tables and Functions)

`__index` is invoked when you **read a key that is not present** in a table (`rawget(t, key) == nil`). It can be a table or a function.

**As a table:** Lua repeats the lookup in that table. This is the basis of classes and inheritance.

```lua
local defaults = { color = "blue", size = 10 }
local item = setmetatable({ size = 20 }, { __index = defaults })

print(item.size)     --> 20     (found in item itself)
print(item.color)    --> blue   (not in item; found in defaults)
print(item.weight)   --> nil    (not found anywhere)
```

The lookup chains: if `defaults` also had a metatable with `__index`, Lua would continue through it.

**As a function:** Lua calls `__index(table, key)` and uses the result. This allows computed or lazily created values:

```lua
local squares = setmetatable({}, {
  __index = function(t, n)
    local value = n * n
    rawset(t, n, value)       -- cache so we compute it only once
    return value
  end
})
print(squares[7])    --> 49
print(rawget(squares, 7))   --> 49 (now stored)
```

`__index` only matters for **missing** keys. Existing keys are returned directly without calling it.

---

## 16.4 `__newindex`

`__newindex` is invoked when you **assign to a key that is not present** in a table. Like `__index`, it can be a table or a function.

**As a function:** `__newindex(table, key, value)`. You decide what to do with the assignment:

```lua
local proxy = setmetatable({}, {
  __newindex = function(t, k, v)
    print("setting " .. tostring(k) .. " = " .. tostring(v))
    rawset(t, k, v)           -- actually store it
  end
})

proxy.a = 1                   --> setting a = 1
proxy.a = 2                   -- no output: key exists now, so __newindex is not called
print(proxy.a)                --> 2
```

**As a table:** the assignment is redirected to that other table:

```lua
local storage = {}
local t = setmetatable({}, { __newindex = storage })
t.x = 5
print(rawget(t, "x"))    --> nil  (not stored in t)
print(storage.x)         --> 5    (stored in storage)
```

Typical uses: validating assignments, tracking changes, read-only tables (16.10), and detecting accidental globals (Lesson 24). Remember that inside a `__newindex` function, assigning `t[k] = v` would call `__newindex` again (infinite recursion). Use `rawset`.

---

## 16.5 `rawget()` and `rawset()`

The `raw` functions access a table **bypassing all metamethods**:

| Function | Purpose |
|---|---|
| `rawget(t, k)` | Read `t[k]` without `__index` |
| `rawset(t, k, v)` | Write `t[k] = v` without `__newindex` |
| `rawequal(a, b)` | Compare without `__eq` |
| `rawlen(t)` | Length without `__len` (Lua 5.2+) |

```lua
local t = setmetatable({}, {
  __index = function() return "default" end,
})
print(t.missing)               --> default
print(rawget(t, "missing"))    --> nil
```

Use them to avoid infinite recursion inside metamethods and to inspect the real contents of a table, e.g. when checking whether a key was actually stored.

---

## 16.6 Arithmetic Metamethods (`__add`, `__sub`, `__mul`, `__div`, etc.)

You can make `+`, `-`, `*`, and the other operators work on tables. When Lua tries an arithmetic operation on a value that is not a number, it checks the **first** operand for the metamethod, then the **second**, and calls it with both operands.

| Metamethod | Operator |
|---|---|
| `__add` | `a + b` |
| `__sub` | `a - b` |
| `__mul` | `a * b` |
| `__div` | `a / b` |
| `__mod` | `a % b` |
| `__pow` | `a ^ b` |
| `__unm` | `-a` (unary minus) |
| `__idiv` | `a // b` (5.3+) |
| `__band`, `__bor`, `__bxor`, `__shl`, `__shr`, `__bnot` | `&`, `\|`, `~`, `<<`, `>>`, unary `~` (5.3+) |
| `__concat` | `a .. b` |

```lua
local Money = {}
Money.__index = Money

local function new_money(cents)
  return setmetatable({ cents = cents }, Money)
end

Money.__add = function(a, b) return new_money(a.cents + b.cents) end
Money.__sub = function(a, b) return new_money(a.cents - b.cents) end
Money.__mul = function(a, k)
  if type(a) == "number" then a, k = k, a end    -- allow 3 * money
  return new_money(a.cents * k)
end
Money.__unm = function(a) return new_money(-a.cents) end
Money.__tostring = function(a)
  return string.format("$%d.%02d", a.cents // 100, a.cents % 100)
end

local price = new_money(1050)
local tip = new_money(200)
print(price + tip)       --> $12.50
print(price - tip)       --> $8.50
print(price * 2)         --> $21.00
print(2 * price)         --> $21.00
print(-tip)              --> $-2.00 (naive formatting of a negative amount)
```

Because either operand may be the table, a metamethod must check which argument is which, as `__mul` does above for `number * money`.

---

## 16.7 Comparison Metamethods (`__eq`, `__lt`, `__le`)

| Metamethod | Operator(s) |
|---|---|
| `__eq` | `a == b` and `a ~= b` |
| `__lt` | `a < b` and `a > b` |
| `__le` | `a <= b` and `a >= b` |

```lua
local Version = {}
Version.__index = Version

local function V(major, minor)
  return setmetatable({ major = major, minor = minor }, Version)
end

Version.__eq = function(a, b) return a.major == b.major and a.minor == b.minor end
Version.__lt = function(a, b)
  if a.major ~= b.major then return a.major < b.major end
  return a.minor < b.minor
end
Version.__le = function(a, b) return a == b or a < b end

print(V(1, 2) == V(1, 2))   --> true   (different tables, equal by __eq)
print(V(1, 2) < V(1, 10))   --> true
print(V(2, 0) >= V(1, 9))   --> true
```

Rules:

- `__eq` is called only when **both** values are tables (or both userdata) and they are not the *same* object. Results are converted to a boolean.
- `a > b` is evaluated as `b < a`, and `a >= b` as `b <= a`, so you define only `__lt` and `__le`.
- In Lua 5.3 and earlier, a missing `__le` was emulated with `not (b < a)`. **Lua 5.4 deprecated that fallback** (it only exists when Lua is built with 5.3 compatibility), so define `__le` yourself when you need `<=`.
- Sorting a list of such objects with `table.sort(list)` uses `__lt` automatically.

---

## 16.8 `__tostring`, `__len`, `__concat`, `__call`

**`__tostring(obj)`** controls the string produced by `tostring` (and so by `print`). It must return a string:

```lua
local p = setmetatable({ x = 1, y = 2 }, {
  __tostring = function(p) return "(" .. p.x .. ", " .. p.y .. ")" end,
})
print(p)                       --> (1, 2)
print("Point: " .. tostring(p))--> Point: (1, 2)
```

**`__len(obj)`** overrides the `#` operator:

```lua
local bag = setmetatable({ items = { "a", "b", "c" } }, {
  __len = function(b) return #b.items end,
})
print(#bag)                    --> 3
```

**`__concat(a, b)`** is used for `..` when an operand is not a string or number:

```lua
local Text = {}
Text.__concat = function(a, b)
  return tostring(a) .. tostring(b)
end
Text.__tostring = function(t) return "<" .. t.s .. ">" end
local t = setmetatable({ s = "hi" }, Text)
print(t .. " there")           --> <hi> there
print("say " .. t)             --> say <hi>
```

**`__call(obj, ...)`** lets you call a table like a function. The table itself is passed as the first argument:

```lua
local Counter = setmetatable({ n = 0 }, {
  __call = function(self, step)
    self.n = self.n + (step or 1)
    return self.n
  end,
})
print(Counter())        --> 1
print(Counter(5))       --> 6
```

`__call` is how class tables become constructors (`Player("Ada")`) and how callable objects such as functors or memoized functions are built.

---

## 16.9 `__pairs`, `__close`, `__gc`, `__mode`, `__metatable`

Several metamethods control special behaviors:

**`__pairs(t)`** (5.2+): called by `pairs(t)`; it must return the same three values as `next, t, nil`, meaning an iterator function, state, and initial control value.

```lua
local ordered = setmetatable({ keys = { "b", "a" }, data = { a = 1, b = 2 } }, {
  __pairs = function(self)
    local i = 0
    return function()
      i = i + 1
      local k = self.keys[i]
      if k then return k, self.data[k] end
    end, self, nil
  end,
})
for k, v in pairs(ordered) do print(k, v) end   --> b 2 / a 1
```

**`__close`** (5.4): called when a `local x <close>` variable goes out of scope. See Lesson 26.

**`__gc`** (5.2+ for tables): a **finalizer** called when the garbage collector is about to free the object. The `__gc` field must be present in the metatable *when `setmetatable` is called*. See Lesson 26.

**`__mode`**: makes a table **weak**: `"k"` for weak keys, `"v"` for weak values, `"kv"` for both. Weak references do not keep objects alive. See Lesson 26.

```lua
local cache = setmetatable({}, { __mode = "v" })   -- values may be collected
```

**`__metatable`**: protects the metatable. `getmetatable(obj)` returns this field's value instead of the real metatable, and `setmetatable(obj, ...)` raises an error:

```lua
local locked = setmetatable({}, { __metatable = "locked" })
print(getmetatable(locked))            --> locked
print(pcall(setmetatable, locked, {})) --> false	cannot change a protected metatable
```

Also worth knowing: **`__name`** (a string used in error messages and by `tostring`, mainly for userdata types), and **`__idiv`/`__band`/...** listed in 16.6.

---

## 16.10 Read-Only Tables and Default Values

**Read-only table.** Keep the real data in a hidden table and expose an empty **proxy** that forwards reads and blocks writes:

```lua
local function readonly(t)
  return setmetatable({}, {
    __index = t,                       -- reads go to the real table
    __newindex = function(_, k)        -- writes are rejected
      error("attempt to modify read-only field '" .. tostring(k) .. "'", 2)
    end,
    __len = function() return #t end,
    __pairs = function() return pairs(t) end,
    __metatable = false,               -- hide/lock the metatable
  })
end

local days = readonly({ "Mon", "Tue", "Wed" })
print(days[2], #days)                  --> Tue	3
print(pcall(function() days[1] = "X" end))
--> false	input:13: attempt to modify read-only field '1'
```

The proxy stays empty, so `__newindex` fires for *every* write. (A read-only table whose contents were stored directly in it would allow writes to existing keys.)

**Default values.** Return a default when a key is missing:

```lua
local function with_default(t, default)
  return setmetatable(t, {
    __index = function() return default end,
  })
end

local counts = with_default({}, 0)
for _, word in ipairs({ "a", "b", "a" }) do
  counts[word] = counts[word] + 1       -- missing keys read as 0
end
print(counts.a, counts.b, counts.z)     --> 2	1	0
```

Notice that reading `counts.z` returns `0` without storing anything. If the default is a table that callers will modify, use a function that creates a fresh table and stores it:

```lua
local groups = setmetatable({}, {
  __index = function(t, k)
    local v = {}
    rawset(t, k, v)
    return v
  end
})
table.insert(groups.fruit, "apple")     -- groups.fruit is created on first use
print(#groups.fruit)                    --> 1
```

---

## 16.11 Operator Overloading Example: Vectors

Putting several metamethods together, here is a 2D vector type:

```lua
local Vector = {}
Vector.__index = Vector

function Vector.new(x, y)
  return setmetatable({ x = x or 0, y = y or 0 }, Vector)
end

function Vector:length()
  return math.sqrt(self.x ^ 2 + self.y ^ 2)
end

function Vector:normalized()
  local len = self:length()
  if len == 0 then return Vector.new(0, 0) end
  return Vector.new(self.x / len, self.y / len)
end

function Vector.dot(a, b)
  return a.x * b.x + a.y * b.y
end

-- Metamethods
Vector.__add = function(a, b) return Vector.new(a.x + b.x, a.y + b.y) end
Vector.__sub = function(a, b) return Vector.new(a.x - b.x, a.y - b.y) end
Vector.__unm = function(a)    return Vector.new(-a.x, -a.y) end

Vector.__mul = function(a, b)
  if type(a) == "number" then return Vector.new(a * b.x, a * b.y) end
  if type(b) == "number" then return Vector.new(a.x * b, a.y * b) end
  return Vector.dot(a, b)                       -- vector * vector = dot product
end

Vector.__div = function(a, k) return Vector.new(a.x / k, a.y / k) end
Vector.__eq  = function(a, b) return a.x == b.x and a.y == b.y end
Vector.__len = function(a) return a:length() end   -- __len may return any value, not just integers
Vector.__tostring = function(a) return "(" .. a.x .. ", " .. a.y .. ")" end

-- Make the class itself callable: Vector(3, 4) instead of Vector.new(3, 4)
setmetatable(Vector, { __call = function(_, x, y) return Vector.new(x, y) end })

local a = Vector(3, 4)
local b = Vector(1, 2)

print(a + b)            --> (4, 6)
print(a - b)            --> (2, 2)
print(a * 2)            --> (6, 8)
print(2 * a)            --> (6, 8)
print(a * b)            --> 11
print(-a)               --> (-3, -4)
print(a / 2)            --> (1.5, 2.0)
print(#a)               --> 5.0
print(a == Vector(3, 4))--> true
print(a:normalized())   --> (0.6, 0.8)
```

This pattern (a table acting as both a class with `__index` and a constructor with `__call`) is the starting point for the object-oriented programming covered in Lessons 17 to 20.

---

[Previous](./[15]-Common-Data-Structures.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[17]-Objects-And-Classes.md)
