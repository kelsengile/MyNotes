[Previous](./[17]-Objects-And-Classes.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[19]-Encapsulation-And-Privacy.md)

*Object-Oriented Programming*

# Lesson 18 - Inheritance & Polymorphism

Inheritance lets one kind of object reuse and extend the behavior of another. Lua has no `extends` keyword; instead, inheritance is just a chain of `__index` lookups. This lesson builds it from the ground up, shows how to override and call parent methods, and ends with a reusable `class()` helper and an overview of popular libraries.

---

## 18.1 What is Inheritance in Lua?

**Inheritance** means that an object (or class) can use the fields and methods of another one without copying them. In Lua this is done with **delegation**: when a key is missing, Lua asks another table (through `__index`) instead of failing. Lesson 16.3 introduced this; Lesson 17 used it to build classes.

Chain two lookups together and you have inheritance:

```lua
local grandparent = { family = "Smith" }
local parent = setmetatable({ job = "farmer" }, { __index = grandparent })
local child  = setmetatable({ name = "Ada" },   { __index = parent })

print(child.name)     --> Ada      (found in child)
print(child.job)      --> farmer   (found in parent)
print(child.family)   --> Smith    (found in grandparent)
print(child.color)    --> nil      (not found anywhere)
```

Lua walks the chain `child -> parent -> grandparent` until it finds the key or runs out of tables. That is the whole mechanism. Everything in this lesson is a way of arranging these chains neatly.

Important points:

- Inheritance is a **lookup**, not a copy. If the parent changes later, the child sees the change.
- **Reads** follow the chain, but **writes** always go to the object itself (unless `__newindex` says otherwise). Writing `child.job = "baker"` creates a field on `child` and leaves `parent` untouched.
- A long chain makes lookups slower, so keep hierarchies shallow.

---

## 18.2 Prototype-Based Inheritance

In a **prototype-based** system there are no classes, only objects. A new object is made by taking an existing object (the **prototype**) and letting the new one fall back to it. This is how the language Self works, and it is very natural in Lua.

```lua
local Account = { balance = 0 }

function Account:new(o)
  o = o or {}
  setmetatable(o, self)    -- self is the prototype
  self.__index = self      -- missing keys are looked up in the prototype
  return o
end

function Account:deposit(amount)
  self.balance = self.balance + amount
end

function Account:show()
  print("Balance: " .. self.balance)
end

local a = Account:new()
a:deposit(100)
a:show()                 --> Balance: 100
print(Account.balance)   --> 0   (the prototype was not changed)
```

Look closely at `self.balance = self.balance + amount`. The **read** finds `balance` in the prototype (`0`), but the **write** creates a new `balance` field on `a`. That is exactly why the prototype stays at `0`.

Because `new` uses `self`, you can call it on *any* object, and the result inherits from that object. A **specialization** of `Account` is just an object made from `Account`:

```lua
local SpecialAccount = Account:new()      -- SpecialAccount inherits from Account

function SpecialAccount:withdraw(amount)
  if amount > self.balance + self.limit then
    print("insufficient funds")
  else
    self.balance = self.balance - amount
  end
end

local s = SpecialAccount:new({ limit = 1000 })   -- s inherits from SpecialAccount

s:deposit(100)           -- found in Account, two steps up the chain
s:withdraw(1000)         -- found in SpecialAccount
s:show()                 --> Balance: -900
```

The chain here is `s -> SpecialAccount -> Account`. Note that `SpecialAccount` is itself an ordinary instance of `Account`; there is no separate "class" concept.

Prototype style is compact, but it mixes "template" and "instance" in one idea, which can confuse readers. The next sections use the more common **class-based** layout, where the class is clearly a separate table from its instances.

---

## 18.3 Subclassing with Metatable Chains

A **subclass** is a class whose table falls back to its **parent class**. Together with the instance-to-class link from Lesson 17, that gives a chain: `instance -> Subclass -> Parent`.

```lua
-- Parent class
local Animal = {}
Animal.__index = Animal

function Animal.new(name, sound)
  local self = setmetatable({}, Animal)
  self.name = name
  self.sound = sound
  return self
end

function Animal:speak()
  print(self.name .. " says " .. self.sound)
end

function Animal:describe()
  print(self.name .. " is an animal")
end

-- Subclass: Dog falls back to Animal
local Dog = setmetatable({}, { __index = Animal })
Dog.__index = Dog

function Dog.new(name)
  local self = Animal.new(name, "Woof")   -- reuse the parent constructor
  return setmetatable(self, Dog)          -- then re-link to the subclass
end

function Dog:fetch()
  print(self.name .. " fetches the ball")
end

local rex = Dog.new("Rex")
rex:speak()      --> Rex says Woof        (found in Animal)
rex:fetch()      --> Rex fetches the ball (found in Dog)
rex:describe()   --> Rex is an animal     (found in Animal)
```

The lookup for `rex:speak()` goes through three tables:

1. `rex` itself: no `speak`.
2. `rex`'s metatable is `Dog`, and `Dog.__index` is `Dog`: no `speak` in `Dog`.
3. `Dog`'s own metatable has `__index = Animal`: `Animal.speak` is found.

There are **two different `__index` roles** here, and they are easy to mix up:

| Where | Setting | Meaning |
|---|---|---|
| `Dog.__index = Dog` | a field *inside* `Dog` | instances of `Dog` look in `Dog` |
| `setmetatable(Dog, { __index = Animal })` | the metatable *of* `Dog` | `Dog` itself looks in `Animal` |

**Checking the type of an object.** Walk the chain and compare each class with the one you are looking for:

```lua
local function is_a(obj, class)
  local mt = getmetatable(obj)
  while mt do
    if mt == class then return true end
    local parent_mt = getmetatable(mt)       -- the metatable of the class
    mt = parent_mt and parent_mt.__index     -- its __index is the parent class
  end
  return false
end

print(is_a(rex, Dog))      --> true
print(is_a(rex, Animal))   --> true
print(is_a(rex, table))    --> false
```

---

## 18.4 Overriding Methods

A subclass **overrides** a method by defining one with the same name. Since lookup stops at the first table that has the key, the subclass version wins and the parent version is never reached.

```lua
local Animal = {}
Animal.__index = Animal
function Animal.new(name) return setmetatable({ name = name }, Animal) end
function Animal:speak() print(self.name .. " makes a sound") end

local Dog = setmetatable({}, { __index = Animal })
Dog.__index = Dog
function Dog.new(name) return setmetatable(Animal.new(name), Dog) end
function Dog:speak() print(self.name .. " barks") end          -- override

local Fish = setmetatable({}, { __index = Animal })
Fish.__index = Fish
function Fish.new(name) return setmetatable(Animal.new(name), Fish) end
-- Fish does not override speak, so it inherits Animal's version

Animal.new("Thing"):speak()   --> Thing makes a sound
Dog.new("Rex"):speak()        --> Rex barks
Fish.new("Nemo"):speak()      --> Nemo makes a sound
```

Overriding affects only the class that defines it. `Animal` and `Fish` are unchanged.

**Metamethods are not inherited through `__index`.** As noted in Lesson 17.7, Lua looks for `__tostring`, `__eq`, `__add`, and the others directly in an object's metatable, without following `__index`. If `Animal` defines `__tostring`, instances of `Dog` will not use it unless `Dog` has it too:

```lua
Animal.__tostring = function(a) return "Animal(" .. a.name .. ")" end

print(Animal.new("Thing"))   --> Animal(Thing)
print((tostring(Dog.new("Rex")):gsub("0x%x+", "0x...")))   --> table: 0x...   (not inherited!)

-- Fix: copy or define it in the subclass
Dog.__tostring = Animal.__tostring
print(Dog.new("Rex"))        --> Animal(Rex)
```

The `class()` helper in 18.7 automates this copying.

---

## 18.5 Calling a Parent Method (Manual `super`)

Often a subclass wants to **extend** a parent method rather than replace it: do the parent's work, then add more. Lua has no built-in `super`, but you can call the parent's function directly and pass `self` yourself:

```lua
local Animal = {}
Animal.__index = Animal

function Animal.new(name)
  return setmetatable({ name = name }, Animal)
end

function Animal:speak()
  return self.name .. " makes a sound"
end

local Dog = setmetatable({}, { __index = Animal })
Dog.__index = Dog

function Dog.new(name, breed)
  local self = Animal.new(name)            -- call the parent constructor
  self.breed = breed                       -- then add Dog's own fields
  return setmetatable(self, Dog)
end

function Dog:speak()
  local base = Animal.speak(self)          -- call the parent method with self
  return base .. " (a loud bark!)"
end

local d = Dog.new("Rex", "Collie")
print(d:speak())   --> Rex makes a sound (a loud bark!)
print(d.breed)     --> Collie
```

Two details matter:

- Use the **dot** form, `Animal.speak(self)`. The colon form `Animal:speak()` would pass `Animal` itself as `self`, which is wrong here.
- Name the parent **explicitly** (`Animal`) instead of looking it up dynamically.

**A pitfall with `self.super`.** It is tempting to store the parent as a field and write `self.super.speak(self)`. That works for one level but breaks with two. Suppose `Puppy` inherits from `Dog`, which inherits from `Animal`, and `Dog:speak` calls `self.super.speak(self)`. When called on a `Puppy` instance, `self.super` resolves to `Puppy.super`, which is `Dog`, so `Dog:speak` calls itself forever and ends in a stack overflow. Referring to the parent class by name (or through a local variable captured when the class is defined) always points at the right class:

```lua
local Puppy = setmetatable({}, { __index = Dog })
Puppy.__index = Puppy

function Puppy.new(name)
  return setmetatable(Dog.new(name, "Mixed"), Puppy)
end

function Puppy:speak()
  return Dog.speak(self) .. " Also, it yips!"   -- explicit parent: Dog
end

print(Puppy.new("Bit"):speak())
--> Bit makes a sound (a loud bark!) Also, it yips!
```

The same explicit style works for constructors: call `Parent.new(...)` (or a `Parent.init(self, ...)` function) first, then add the subclass fields, as `Dog.new` did above.

---

## 18.6 Polymorphism and Duck Typing

**Polymorphism** means that the same call does different things depending on the object. With overriding, one loop can handle many kinds of animals:

```lua
local Animal = {}
Animal.__index = Animal
function Animal.new(name) return setmetatable({ name = name }, Animal) end
function Animal:speak() return self.name .. " makes a sound" end

local Dog = setmetatable({}, { __index = Animal })
Dog.__index = Dog
function Dog.new(name) return setmetatable(Animal.new(name), Dog) end
function Dog:speak() return self.name .. " barks" end

local Cat = setmetatable({}, { __index = Animal })
Cat.__index = Cat
function Cat.new(name) return setmetatable(Animal.new(name), Cat) end
function Cat:speak() return self.name .. " meows" end

local pets = { Dog.new("Rex"), Cat.new("Tom"), Animal.new("Thing") }
for _, pet in ipairs(pets) do
  print(pet:speak())          -- the right version runs for each object
end
--> Rex barks
--> Tom meows
--> Thing makes a sound
```

The loop never asks "what kind of animal is this?" It just calls `speak`, and Lua finds the correct method.

**Duck typing.** Lua does not check types when you call a method. If an object has a `speak` method, the call works, whatever the object is: "if it walks like a duck and quacks like a duck, it is a duck." So polymorphism does not even require a shared parent class:

```lua
local Robot = {}
Robot.__index = Robot
function Robot.new(id) return setmetatable({ id = id }, Robot) end
function Robot:speak() return "Unit " .. self.id .. " beeps" end

local crowd = { Dog.new("Rex"), Robot.new(7) }
for _, thing in ipairs(crowd) do
  print(thing:speak())
end
--> Rex barks
--> Unit 7 beeps
```

`Robot` shares no ancestor with `Dog`, yet both work with the same loop.

Because nothing is checked in advance, a missing method shows up only when the call runs ("attempt to call a nil value (method 'speak')"). If you want a friendlier check, test for the method first:

```lua
local function make_speak(thing)
  if type(thing.speak) ~= "function" then
    return "(this thing cannot speak)"
  end
  return thing:speak()
end

print(make_speak(Dog.new("Rex")))   --> Rex barks
print(make_speak({}))               --> (this thing cannot speak)
```

Lesson 19.6 turns this idea into reusable interface checks.

---

## 18.7 A Reusable `class()` Helper

Writing the `__index` lines, parent links, and constructors by hand for every class gets repetitive. A small helper can do it once. Here is a compact one:

```lua
local function class(parent)
  local cls = {}
  cls.__index = cls                       -- instances look in the class

  -- Metamethods are not inherited via __index, so copy the parent's
  if parent then
    for key, value in pairs(parent) do
      if type(key) == "string" and key:sub(1, 2) == "__" and key ~= "__index" then
        cls[key] = value
      end
    end
  end

  setmetatable(cls, {
    __index = parent,                     -- the class looks in its parent
    __call = function(c, ...)             -- Class(...) creates an instance
      local obj = setmetatable({}, c)
      if obj.init then obj:init(...) end  -- run the constructor if there is one
      return obj
    end,
  })
  return cls
end

local function is_a(obj, cls)
  local mt = getmetatable(obj)
  while mt do
    if mt == cls then return true end
    local meta_of_class = getmetatable(mt)
    mt = meta_of_class and meta_of_class.__index
  end
  return false
end
```

Using it:

```lua
local Animal = class()

function Animal:init(name)
  self.name = name
end

function Animal:speak()
  return self.name .. " makes a sound"
end

Animal.__tostring = function(a) return "Animal(" .. a.name .. ")" end

local Dog = class(Animal)                 -- Dog inherits from Animal

function Dog:init(name, breed)
  Animal.init(self, name)                 -- explicit call to the parent constructor
  self.breed = breed
end

function Dog:speak()
  return Animal.speak(self) .. " (woof!)"
end

local d = Dog("Rex", "Collie")            -- no .new needed
print(d:speak())                          --> Rex makes a sound (woof!)
print(d)                                  --> Animal(Rex)   (__tostring was copied)
print(is_a(d, Dog), is_a(d, Animal))      --> true	true
print(is_a(Animal("Thing"), Dog))         --> false
```

What the helper gives you:

- `class()` creates a base class and `class(Parent)` creates a subclass.
- The class is **callable**: `Dog("Rex")` builds the instance and calls `init`.
- Metamethods such as `__tostring` and `__eq` that exist on the parent when you create the subclass are copied, so they work on subclass instances.

Limitations to be aware of:

- Metamethods are copied at the moment the subclass is created. Define the parent's metamethods **before** calling `class(Parent)`.
- The helper does not provide multiple inheritance (Lesson 20) or mixins, and its `is_a` only follows single-parent chains.
- A subclass calls the parent constructor explicitly (`Animal.init(self, ...)`), as discussed in 18.5.

---

## 18.8 Popular Class Libraries (`middleclass`, `30log`, `classic`)

Many Lua projects use a small library instead of a hand-written helper. All three below are installable with LuaRocks (Lesson 3) or by copying a single file into your project. The snippets show their typical usage; check each project's README for the complete, current API.

**`classic`** (by rxi) is tiny, about 50 lines. It is popular with LÖVE developers.

```lua
local Object = require("classic")

local Point = Object:extend()
function Point:new(x, y)                       -- constructor is named "new"
  self.x = x or 0
  self.y = y or 0
end

local Rect = Point:extend()
function Rect:new(x, y, w, h)
  Rect.super.new(self, x, y)                   -- call the parent constructor
  self.w = w or 0
  self.h = h or 0
end

local r = Rect(10, 20, 100, 50)                -- classes are callable
print(r:is(Point))                             --> true
```

**`middleclass`** (by kikito) is feature-rich: named classes, mixins, `isInstanceOf`, and inherited metamethods.

```lua
local class = require("middleclass")

local Animal = class("Animal")
function Animal:initialize(name) self.name = name end
function Animal:speak() return self.name .. " makes a sound" end

local Dog = class("Dog", Animal)               -- or Animal:subclass("Dog")
function Dog:initialize(name)
  Animal.initialize(self, name)                -- call the parent constructor
end
function Dog:speak() return self.name .. " barks" end

local d = Dog:new("Rex")                       -- Dog("Rex") also works
print(d:speak())                               --> Rex barks
print(d:isInstanceOf(Animal))                  --> true
print(Dog:isSubclassOf(Animal))                --> true
```

**`30log`** (by Yonaba) is small and flexible, with `extend`, `instanceOf`, and optional mixins.

```lua
local class = require("30log")

local Animal = class("Animal")
function Animal:init(name) self.name = name end

local Dog = Animal:extend("Dog")
function Dog:init(name)
  Dog.super.init(self, name)                   -- call the parent constructor
  self.sound = "Woof"
end

local d = Dog:new("Rex")
print(d:instanceOf(Animal))                    --> true
```

How to choose:

| Library | Strengths | Constructor name |
|---|---|---|
| `classic` | Very small, easy to read and vendor into a project | `new` |
| `middleclass` | Mixins, class names, inherited metamethods, widely used | `initialize` |
| `30log` | Small, readable, flexible class creation | `init` |

Whichever you choose, the ideas from this lesson still apply: they are all built on `__index` chains, `setmetatable`, and a callable class table. Understanding the manual version makes the libraries easy to learn, and easy to replace if you ever need to.

---

[Previous](./[17]-Objects-And-Classes.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[19]-Encapsulation-And-Privacy.md)
