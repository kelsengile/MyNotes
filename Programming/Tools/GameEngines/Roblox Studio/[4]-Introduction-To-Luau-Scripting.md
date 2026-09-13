[Previous](./[3]-The-Roblox-Instance-Hierarchy.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[5]-Server-Scripts,-Local-Scripts,-And-RemoteEvents.md)

*Building And Scripting*

# Lesson 4 - Introduction To Luau Scripting

## 4.1 Luau vs Standard Lua

Luau is Roblox's own dialect of Lua — it's built on Lua 5.1 syntax but adds **optional static typing**, performance improvements, and a set of additional standard-library features not found in vanilla Lua. Every script you write in Studio, whether a `Script`, `LocalScript`, or `ModuleScript`, is written in Luau.

If you've used Lua elsewhere, almost everything transfers directly — the main things to know as Roblox-specific:

- Luau adds optional type annotations (`local health: number = 100`), checked by Studio's built-in type checker/linter, though they're never required.
- Luau includes extra built-in functions and table utilities beyond standard Lua (`table.find`, `string.split`, etc.), so not every "Lua tutorial" you find online will use idioms Roblox actually recommends.
- Arrays in Lua/Luau are 1-indexed, not 0-indexed — the first element of a table is `myTable[1]`, not `myTable[0]`. This trips up people coming from most other languages.

---

## 4.2 Variables, Types, And Control Flow

Variables are declared with `local` (always prefer `local` over global variables — globals leak across scripts and hurt performance):

```lua
local health = 100
local playerName = "Hero"
local speed: number = 16   -- optional type annotation
```

Luau's basic types include `number`, `string`, `boolean`, `nil`, `table`, and Roblox-specific types like `Instance`, `Vector3`, and `CFrame` (covered in Lesson 7.4).

Control flow uses `then`/`end` blocks rather than braces:

```lua
if health <= 0 then
    print("Player defeated")
elseif health < 20 then
    print("Low health warning")
else
    print("Health is fine")
end

for i = 1, 5 do
    print("Count:", i)
end

local items = {"sword", "shield", "potion"}
for index, item in ipairs(items) do
    print(index, item)
end
```

---

## 4.3 Functions And Tables

Functions are declared with `function`/`end`:

```lua
local function takeDamage(currentHealth, amount)
    return currentHealth - amount
end

local newHealth = takeDamage(health, 25)
```

**Tables** are Luau's single all-purpose data structure — used as arrays, dictionaries, and even as the basis for object-oriented patterns:

```lua
-- As an array
local inventory = {"sword", "shield", "potion"}

-- As a dictionary
local playerStats = {
    health = 100,
    speed = 16,
    isAlive = true
}

print(playerStats.health)      -- dot access
print(playerStats["health"])   -- equivalent bracket access
```

Combining functions with tables is how Luau approximates classes/objects, since it has no built-in class syntax:

```lua
local Character = {}
Character.__index = Character

function Character.new(name, health)
    local self = setmetatable({}, Character)
    self.name = name
    self.health = health
    return self
end

function Character:takeDamage(amount)
    self.health -= amount
end

local hero = Character.new("Hero", 100)
hero:takeDamage(20)
print(hero.health)   -- 80
```

This pattern (a table + `setmetatable` + `:` method syntax) is standard practice for anything resembling reusable "classes" in Roblox scripting, and you'll see it constantly in ModuleScripts (Lesson 5.2).

---

## 4.4 The Script Editor And Output Window

Studio's built-in **Script Editor** opens automatically when you double-click a Script/LocalScript/ModuleScript Instance in the Explorer. It includes syntax highlighting, autocomplete for Roblox's API, and inline type-checking warnings as you type.

The **Output window** (View > Output) is where `print()` statements and errors/warnings appear while testing — functionally identical in purpose to Godot's Output panel from Lesson 2.3 of the Godot Topic:

```lua
print("Script started")
warn("This shows as a yellow warning")
error("This stops the script and shows as a red error")
```

A few debugging habits worth building early:

- Liberal use of `print()` while learning is completely normal — it's the fastest way to confirm a script reached a given line, or to inspect a variable's value.
- Errors in the Output window include the script name and line number, and clicking the error jumps you directly to that line in the Script Editor.
- The **Output** window is filtered by default to show only the currently selected test context (server or a specific client) when in multi-client test mode (Lesson 2.4) — worth remembering if a `print()` you expected to see seems to be "missing."

[Previous](./[3]-The-Roblox-Instance-Hierarchy.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[5]-Server-Scripts,-Local-Scripts,-And-RemoteEvents.md)
