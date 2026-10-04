[Previous](./[16]-Metatables-And-Metamethods.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[18]-Inheritance-And-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 17 - Objects & Classes with Tables

Lua has no built-in `class` keyword. Instead it gives you tables, functions, and metatables, and from those you can build exactly the object system you want. This lesson builds classes step by step; later lessons add inheritance, privacy, and mixins.

---

## 17.1 Objects as Tables with Functions

An **object** bundles data (fields) with behavior (functions). A table can hold both:

```lua
local dog = {
  name = "Rex",
  sound = "Woof",
}

function dog.speak()
  print(dog.name .. " says " .. dog.sound)
end

dog.speak()   --> Rex says Woof
```

This works, but `speak` is hard-wired to the variable `dog`. If you copy the function to another object, it still talks about the original:

```lua
local cat = { name = "Tom", sound = "Meow", speak = dog.speak }
cat.speak()   --> Rex says Woof   (not Tom!)
```

The fix is for methods to receive the object they should operate on. That's the next topic.

---

## 17.2 The `self` Parameter and Colon Syntax

Pass the object as the first parameter, conventionally named `self`:

```lua
local dog = { name = "Rex", sound = "Woof" }

function dog.speak(self)
  print(self.name .. " says " .. self.sound)
end

dog.speak(dog)    --> Rex says Woof
```

The **colon** syntax does this automatically in both definition and call:

```lua
function dog:speak()                 -- same as dog.speak(self)
  print(self.name .. " says " .. self.sound)
end

dog:speak()                          -- same as dog.speak(dog)
```

Now the same function works for any object that has the right fields:

```lua
local cat = { name = "Tom", sound = "Meow", speak = dog.speak }
cat:speak()   --> Tom says Meow
```

Remember: `obj:method(a)` is exactly `obj.method(obj, a)`. Forgetting the colon (writing `obj.method(a)`) passes `a` as `self`, a very common bug (Lesson 9.10).

Copying `speak` into every object wastes memory and is tedious. A class solves that.

---

## 17.3 Building a Class with `__index`

A **class** is a table that holds the methods shared by all its **instances**. Each instance holds only its own data and has the class set as its `__index` fallback, so a method that is not found in the instance is looked up in the class.

```lua
local Animal = {}           -- the class table
Animal.__index = Animal     -- missing keys on instances are looked up in Animal

function Animal.new(name, sound)
  local self = setmetatable({}, Animal)   -- the instance, linked to the class
  self.name = name
  self.sound = sound
  return self
end

function Animal:speak()
  print(self.name .. " says " .. self.sound)
end

local rex = Animal.new("Rex", "Woof")
local tom = Animal.new("Tom", "Meow")

rex:speak()   --> Rex says Woof
tom:speak()   --> Tom says Meow
print(rex.speak == tom.speak)   --> true (one shared function)
```

How `rex:speak()` is resolved:

1. Lua looks for `speak` in `rex`, which is not found there.
2. `rex` has a metatable (`Animal`) whose `__index` is `Animal`, so Lua looks in `Animal`.
3. It finds `Animal.speak` and calls it with `rex` as `self`.

Setting `Animal.__index = Animal` makes the class double as the metatable; this is the common idiom. The alternative is a separate metatable table, but sharing the class table is shorter.

---

## 17.4 Constructors (`new`)

By convention, the constructor is a function named `new` that creates, initializes, and returns an instance. Two common styles:

**1. Function on the class (dot call):**

```lua
local Rectangle = {}
Rectangle.__index = Rectangle

function Rectangle.new(width, height)
  return setmetatable({ width = width or 1, height = height or 1 }, Rectangle)
end

function Rectangle:area() return self.width * self.height end

local r = Rectangle.new(3, 4)
print(r:area())   --> 12
```

**2. Method with colon (`Class:new`)**, where `self` is the class. This style makes inheritance easier (Lesson 18) because `self` can be a subclass:

```lua
local Circle = {}
Circle.__index = Circle

function Circle:new(radius)
  local obj = setmetatable({}, self)   -- self is the class (or subclass)
  self.__index = self
  obj.radius = radius or 1
  return obj
end

function Circle:area() return math.pi * self.radius ^ 2 end

local c = Circle:new(2)
print(string.format("%.2f", c:area()))   --> 12.57
```

**Making the class callable** so you can write `Rectangle(3, 4)` instead of `Rectangle.new(3, 4)`:

```lua
setmetatable(Rectangle, {
  __call = function(cls, ...) return cls.new(...) end,
})
print(Rectangle(2, 5):area())   --> 10
```

Validate constructor arguments to catch mistakes early:

```lua
function Rectangle.new(width, height)
  assert(type(width) == "number" and width > 0, "width must be a positive number")
  assert(type(height) == "number" and height > 0, "height must be a positive number")
  return setmetatable({ width = width, height = height }, Rectangle)
end
```

---

## 17.5 Instance Fields vs Class Fields

- **Instance fields** are stored in each object and are unique to it (`self.name`).
- **Class fields** are stored in the class table and are shared by all instances (like constants or counters).

```lua
local Player = {}
Player.__index = Player

Player.count = 0               -- class field (shared)
Player.max_health = 100        -- class field used as a default

function Player.new(name)
  Player.count = Player.count + 1
  return setmetatable({ name = name }, Player)   -- instance field: name
end

local a = Player.new("Ann")
local b = Player.new("Bo")
print(Player.count)            --> 2
print(a.max_health)            --> 100   (found in the class via __index)
```

**Reads fall through, writes do not.** Reading `a.max_health` finds the class value, but assigning `a.max_health = 50` creates a new instance field that shadows the class one, leaving the class and other instances unchanged:

```lua
a.max_health = 50
print(a.max_health, b.max_health, Player.max_health)   --> 50	100	100
```

That behavior is handy for per-instance overrides, but it means that to change a class field you must assign through the class (`Player.count = Player.count + 1`), not through an instance (`self.count = self.count + 1` would create an instance field).

**Mutable class fields are shared.** Putting a table in the class and mutating it through an instance affects every instance:

```lua
local Bag = {}
Bag.__index = Bag
Bag.items = {}                         -- BUG: one shared table

function Bag.new() return setmetatable({}, Bag) end
function Bag:add(x) table.insert(self.items, x) end

local b1, b2 = Bag.new(), Bag.new()
b1:add("apple")
print(#b2.items)                       --> 1   (b2 sees b1's item!)
```

Fix: create mutable fields in the constructor so each instance gets its own:

```lua
function Bag.new() return setmetatable({ items = {} }, Bag) end
```

---

## 17.6 Methods and Class-Level Functions

There are three kinds of functions you can attach to a class:

| Kind | Defined as | Called as | Receives |
|---|---|---|---|
| **Instance method** | `function Class:name()` | `obj:name()` | the instance as `self` |
| **Class-level function** (static) | `function Class.name()` | `Class.name()` | no `self` |
| **Class method** | `function Class:name()` | `Class:name()` | the class as `self` |

```lua
local Temperature = {}
Temperature.__index = Temperature

-- Class-level (static) helper: needs no instance
function Temperature.c_to_f(c)
  return c * 9 / 5 + 32
end

-- Constructor
function Temperature.new(celsius)
  return setmetatable({ celsius = celsius }, Temperature)
end

-- Instance methods
function Temperature:to_fahrenheit()
  return Temperature.c_to_f(self.celsius)
end

function Temperature:warm_up(amount)
  self.celsius = self.celsius + amount
  return self                       -- return self to allow method chaining
end

print(Temperature.c_to_f(100))             --> 212.0
local t = Temperature.new(20)
print(t:warm_up(5):warm_up(5):to_fahrenheit())   --> 86.0
```

**Private helper functions** that need no access from outside can simply be local functions in the same file (they never appear on the class):

```lua
local function clamp(x, lo, hi) return math.max(lo, math.min(hi, x)) end

function Temperature:set(c) self.celsius = clamp(c, -273.15, 1e6) end
```

Returning `self` from setter-style methods enables chaining, as in `t:warm_up(5):warm_up(5)`.

---

## 17.7 `__tostring` and Other Metamethods on Classes

Put metamethods in the class table. As the class is the instances' metatable, the metamethods apply to every instance.

```lua
local Point = {}
Point.__index = Point

function Point.new(x, y) return setmetatable({ x = x, y = y }, Point) end

Point.__tostring = function(p) return "Point(" .. p.x .. ", " .. p.y .. ")" end
Point.__eq = function(a, b) return a.x == b.x and a.y == b.y end
Point.__add = function(a, b) return Point.new(a.x + b.x, a.y + b.y) end
Point.__lt = function(a, b) return a.x < b.x or (a.x == b.x and a.y < b.y) end

local p, q = Point.new(1, 2), Point.new(1, 2)
print(p)                    --> Point(1, 2)
print(p == q)               --> true
print(p + q)                --> Point(2, 4)
print(Point.new(0, 5) < p)  --> true
```

Two important details:

1. Metamethod lookup uses the object's metatable **directly**, with raw access. It does **not** follow `__index`. So with inheritance (Lesson 18), a subclass must have its own copy of `__tostring`, `__eq`, etc. if you want them to apply to subclass instances. A class helper can copy them over.
2. `__index` itself is a field that must exist on the metatable (`Point.__index = Point`), which is why it appears at the top of every class.

A useful habit is to give every class a `__tostring`, since printing instances while debugging otherwise shows only `table: 0x...`.

```lua
local plain = setmetatable({}, {})
print((tostring(plain):gsub("0x%x+", "0x...")))   --> table: 0x...
```

---

[Previous](./[16]-Metatables-And-Metamethods.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[18]-Inheritance-And-Polymorphism.md)
