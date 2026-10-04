[Previous](./[19]-Encapsulation-And-Privacy.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[21]-Iterators-And-Generators.md)

*Object-Oriented Programming*

# Lesson 20 - Multiple Inheritance & Mixins

Sometimes an object needs behavior from more than one source: a "named account" that is both a `Named` thing and an `Account`. Lua can model this in several ways. This lesson shows true multiple inheritance, explains exactly how methods are looked up, and then introduces mixins and composition, which are often simpler and safer.

---

## 20.1 Multiple Inheritance with `__index` Functions

So far `__index` has been a **table**, which gives a single parent. When `__index` is a **function**, you decide how to search, so it can look through a **list** of parents:

```lua
-- Look up `key` in each parent, in order; return the first value found
local function search(key, parents)
  for i = 1, #parents do
    local value = parents[i][key]
    if value ~= nil then return value end
  end
end

local function create_class(...)
  local parents = { ... }
  local cls = {}

  -- the class looks in all its parents
  setmetatable(cls, {
    __index = function(_, key) return search(key, parents) end,
  })

  cls.__index = cls                        -- instances look in the class

  function cls.new(o)
    return setmetatable(o or {}, cls)
  end

  return cls
end
```

Now build two independent classes and combine them:

```lua
local Named = {}
function Named:get_name() return self.name end
function Named:set_name(n) self.name = n end

local Account = {}
Account.__index = Account
function Account:deposit(v) self.balance = (self.balance or 0) + v end
function Account:get_balance() return self.balance or 0 end

local NamedAccount = create_class(Account, Named)

local acct = NamedAccount.new({ name = "Paul" })
print(acct:get_name())      --> Paul            (from Named)
acct:deposit(50)            -- from Account
print(acct:get_balance())   --> 50
```

How `acct:get_name()` is found:

1. `acct` has no `get_name`.
2. `acct`'s metatable is `NamedAccount`, and `NamedAccount.__index` is `NamedAccount`, so Lua looks there: no `get_name`.
3. `NamedAccount` has its own metatable with the function `__index`, so Lua calls it. The function tries `Account` (which does not have it) and then `Named` (which does).

Because the search function reads `parents[i][key]`, each parent can itself have a metatable and parents of its own, so multiple inheritance combines naturally with ordinary single inheritance.

**Speeding it up.** The function runs on every missing key. A common optimization is to **copy** the found value into the class, so the next lookup is a plain table hit:

```lua
setmetatable(cls, {
  __index = function(t, key)
    local value = search(key, parents)
    t[key] = value                         -- cache it in the class
    return value
  end,
})
```

The cost: if a parent method is changed later, the cached copy in the subclass is stale. Cache only when parents are fixed after the program starts.

---

## 20.2 Method Lookup Order

With several parents, **order matters**. The search function above is **depth-first, left-to-right**: it asks the first parent (and everything that parent inherits from) before it ever asks the second.

Parent order decides conflicts:

```lua
local Walker = {}
function Walker:move() return "walks" end

local Swimmer = {}
function Swimmer:move() return "swims" end

local WalkerFirst = create_class(Walker, Swimmer)
local SwimmerFirst = create_class(Swimmer, Walker)

print(WalkerFirst.new():move())    --> walks
print(SwimmerFirst.new():move())   --> swims
```

Both parents define `move`, so the one listed first wins. You can still reach the other explicitly by naming it:

```lua
local Amphibian = create_class(Walker, Swimmer)
function Amphibian:move_both()
  return Walker.move(self) .. " and " .. Swimmer.move(self)
end
print(Amphibian.new():move_both())   --> walks and swims
```

**The diamond problem.** Trouble appears when two parents share a common ancestor. Here `D` inherits from `B` and `C`, which both inherit from `A`, and only `C` overrides `hello`:

```lua
local A = {}
function A:hello() return "hello from A" end

local B = create_class(A)               -- B inherits from A, no override
local C = create_class(A)
function C:hello() return "hello from C" end   -- C overrides A's method

local D = create_class(B, C)            -- D inherits from B first, then C

print(D.new():hello())                  --> hello from A
```

You might expect `C`'s more specific version, but the depth-first search goes into `B` first, and `B` finds `A.hello` *through its own parent* before `C` is ever consulted. Languages such as Python avoid this with a smarter ordering algorithm (C3 linearization); this simple Lua search does not.

Ways to deal with it:

- **Reorder the parents**: `create_class(C, B)` finds `C.hello` first.
- **Define the method on the child** and decide explicitly what to call:

```lua
function D:hello() return C.hello(self) end
```

- **Avoid diamonds.** Prefer mixins (20.3) or composition (20.4), where each piece has no hidden ancestors.

**Debugging lookups.** When you are unsure which class a method comes from, write a helper that walks the parents and reports the first match:

```lua
local function find_owner(cls, key, parents_of)
  if rawget(cls, key) ~= nil then return cls end
  for _, parent in ipairs(parents_of[cls] or {}) do
    local owner = find_owner(parent, key, parents_of)
    if owner then return owner end
  end
end

local names = {}                           -- tag each class with a name for printing
names[A], names[B], names[C], names[D] = "A", "B", "C", "D"
local parents_of = { [B] = { A }, [C] = { A }, [D] = { B, C } }

print(names[find_owner(D, "hello", parents_of)])   --> A
```

This repeats the same depth-first order as the search function, so it tells you what Lua will find.

---

## 20.3 Mixins and Composition

A **mixin** is a small table of functions that you **copy into** a class to add behavior. Unlike multiple inheritance, nothing is searched at run time: after copying, the methods are ordinary members of the class. There are no hidden parents and no diamond problem.

```lua
local function include(cls, mixin)
  for key, value in pairs(mixin) do
    if cls[key] == nil then            -- do not overwrite what the class already has
      cls[key] = value
    end
  end
  return cls
end

-- Mixins: reusable behavior with no class of their own
local Walks = {
  walk = function(self) return self.name .. " walks" end,
}

local Swims = {
  swim = function(self) return self.name .. " swims" end,
}

local Speaks = {
  speak = function(self) return self.name .. " says " .. (self.sound or "...") end,
}
```

Add them to ordinary classes:

```lua
local Duck = {}
Duck.__index = Duck
function Duck.new(name) return setmetatable({ name = name, sound = "Quack" }, Duck) end

include(Duck, Walks)
include(Duck, Swims)
include(Duck, Speaks)

local Dog = {}
Dog.__index = Dog
function Dog.new(name) return setmetatable({ name = name, sound = "Woof" }, Dog) end

include(Dog, Walks)
include(Dog, Speaks)                  -- dogs, in this example, do not swim

local d = Duck.new("Donald")
print(d:walk())    --> Donald walks
print(d:swim())    --> Donald swims
print(d:speak())   --> Donald says Quack

local rex = Dog.new("Rex")
print(rex:speak())          --> Rex says Woof
print(rex.swim)             --> nil
```

Because the mixin functions are copied into the class table, lookups are fast and the mixin can be used by classes that have nothing in common. The class decides which pieces it wants.

Guidelines for good mixins:

- A mixin should rely only on a **small, documented set of fields or methods** of `self` (here, `name` and optionally `sound`). Write that requirement next to the mixin.
- If a mixin needs per-object state, give it an initializer that the class calls from its constructor:

```lua
local Counts = {
  init_counts = function(self) self.counts = {} end,
  count = function(self, key)
    self.counts[key] = (self.counts[key] or 0) + 1
    return self.counts[key]
  end,
}

local Page = {}
Page.__index = Page
include(Page, Counts)
function Page.new()
  local self = setmetatable({}, Page)
  self:init_counts()
  return self
end

local pg = Page.new()
pg:count("visit")
print(pg:count("visit"))   --> 2
```

- Copying happens **once**, when you call `include`. Later changes to the mixin table do not reach classes that already included it.
- Metamethods can be mixed in too, since they are copied onto the class itself (which is the metatable for its instances):

```lua
local Printable = {
  __tostring = function(self) return "<" .. (self.name or "?") .. ">" end,
}
include(Dog, Printable)
print(rex)         --> <Rex>
```

**Composition** is the other half of this idea. Instead of inheriting behavior, an object **contains** other objects and hands work to them. This is covered in the next section.

---

## 20.4 Composition vs Inheritance

Inheritance describes an **"is-a"** relationship (a `Dog` is an `Animal`). Composition describes a **"has-a"** relationship (a `Car` has an `Engine`). Both reuse code, but they behave differently as a program grows.

Inheritance builds deep trees that are hard to change: altering a parent can break every descendant. Composition builds objects from small, independent parts that can be swapped.

A composition example, with parts that each do one job:

```lua
-- Components: each one is a small independent class
local Engine = {}
Engine.__index = Engine
function Engine.new(power) return setmetatable({ power = power, running = false }, Engine) end
function Engine:start() self.running = true; return "engine started (" .. self.power .. " hp)" end
function Engine:stop() self.running = false; return "engine stopped" end

local Radio = {}
Radio.__index = Radio
function Radio.new() return setmetatable({ station = 90.5 }, Radio) end
function Radio:tune(s) self.station = s; return "tuned to " .. s end

-- The composite object holds its components
local Car = {}
Car.__index = Car

function Car.new(power)
  return setmetatable({
    engine = Engine.new(power),      -- has-a Engine
    radio = Radio.new(),             -- has-a Radio
  }, Car)
end

-- Delegation: Car exposes its own methods and forwards the work
function Car:start() return self.engine:start() end
function Car:stop()  return self.engine:stop() end
function Car:tune(s) return self.radio:tune(s) end

local car = Car.new(150)
print(car:start())      --> engine started (150 hp)
print(car:tune(101.1))  --> tuned to 101.1
print(car.engine.running)   --> true
```

Swapping a part is easy: give `Car.new` a different engine object that offers `start` and `stop`, and `Car` works unchanged (duck typing again).

**Entity-component style.** Games often take composition further: an entity is just a table of components, and behavior is chosen by which components are present.

```lua
local function new_entity(components)
  return { components = components or {} }
end

local function has(entity, ...)
  for i = 1, select("#", ...) do
    if not entity.components[select(i, ...)] then return false end
  end
  return true
end

local player = new_entity({ position = { x = 0, y = 0 }, health = { hp = 100 }, input = {} })
local rock   = new_entity({ position = { x = 5, y = 5 } })

for _, e in ipairs({ player, rock }) do
  if has(e, "position", "health") then
    print("damageable thing at", e.components.position.x, e.components.position.y)
  end
end
--> damageable thing at	0	0
```

Neither a `Player` class nor a `Rock` class was needed; adding `health` to the rock would make it damageable without touching any class.

**Choosing between them:**

| Situation | Prefer |
|---|---|
| A true "is-a" relationship with shared behavior and a stable hierarchy | Inheritance |
| Reusing the same small behavior in unrelated classes | Mixins |
| An object is made of parts that may change or be swapped | Composition |
| You need many combinations of features (flying, swimming, armored, ...) | Composition or mixins |
| You are tempted to use multiple inheritance | Try mixins or composition first |

A good rule of thumb is **"favor composition over inheritance."** Start with plain tables and functions. Add a class when you need several similar objects, add inheritance when two classes truly share a core identity, and reach for mixins or composition to share everything else. Lua's flexibility lets you change your mind later without rewriting everything.

---

[Previous](./[19]-Encapsulation-And-Privacy.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[21]-Iterators-And-Generators.md)
