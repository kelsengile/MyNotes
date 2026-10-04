[Previous](./[6]-Operators-And-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[8]-Loops.md)

*Core Syntax*

# Lesson 7 - Conditionals: if, elseif, else

Conditionals let your program make decisions: run some code only when a condition holds. Lua offers the `if` statement and, since it has no `switch`, a few table-based patterns to take its place.

---

## 7.1 The `if` Statement

The basic form:

```lua
if condition then
  -- runs when condition is true
end
```

```lua
local temperature = 31
if temperature > 30 then
  print("It's hot!")
end
```

Syntax points:

- Parentheses around the condition are optional (and usually omitted).
- `then` is required after the condition, and `end` closes the block.
- Indentation is for readability only; Lua does not care about it.
- A condition can be any expression, because every value is either truthy or falsy (see 7.4).

```lua
if temperature > 30 then print("hot") end   -- fine on one line
```

---

## 7.2 `elseif` and `else`

Add alternatives with `elseif` (one word) and a fallback with `else`:

```lua
local score = 72

if score >= 90 then
  print("A")
elseif score >= 80 then
  print("B")
elseif score >= 70 then
  print("C")
else
  print("F")
end
--> C
```

Conditions are checked from top to bottom, and **only the first matching branch runs**. Order matters: put the most specific or most restrictive tests first.

**Watch out:** `elseif` is one word. Writing `else if` (two words) starts a *new, nested* `if` that needs its own `end`:

```lua
if score >= 90 then
  print("A")
else if score >= 80 then   -- this opens a second if
  print("B")
end                        -- closes the inner if
end                        -- closes the outer if (easy to forget!)
```

---

## 7.3 Nested Conditionals

Conditionals can be placed inside one another:

```lua
local age, has_ticket = 20, true

if age >= 18 then
  if has_ticket then
    print("Welcome in")
  else
    print("Please buy a ticket")
  end
else
  print("Adults only")
end
```

Deep nesting gets hard to read. You can often flatten it with `and`/`or` or guard clauses (7.7):

```lua
if age >= 18 and has_ticket then
  print("Welcome in")
elseif age >= 18 then
  print("Please buy a ticket")
else
  print("Adults only")
end
```

Combine conditions with `and`, `or`, and `not`, using parentheses for clarity:

```lua
local is_weekend, is_holiday = false, true
if is_weekend or is_holiday then
  print("No work today")
end
```

---

## 7.4 Truthy and Falsy Values in Conditions

In a condition, **only `nil` and `false` are false**. Everything else, including `0` and the empty string, counts as true.

```lua
if 0 then print("0 is true") end              --> 0 is true
if "" then print("empty string is true") end  --> empty string is true
if {} then print("empty table is true") end   --> empty table is true
if nil then print("never") end                -- nothing printed
```

This enables concise checks:

```lua
local config = { name = "app" }

if config.name then           -- "does the field exist?"
  print("name is set")
end

if not config.port then       -- "is the field missing?"
  print("no port set")
end
```

Be careful when a legitimate value can be `false`. `if not enabled then` cannot tell "enabled is false" from "enabled was never set". Compare explicitly with `nil` when it matters:

```lua
local options = { sound = false }

if options.sound == nil then
  print("sound not configured")
elseif options.sound == false then
  print("sound turned off")
end
--> sound turned off
```

Likewise, check numbers explicitly: `if count == 0 then`, not `if not count then`.

---

## 7.5 Simulating Ternary Expressions

Lua has no `condition ? a : b`. For simple cases, use the `and`/`or` idiom (Lesson 6.4):

```lua
local x = 7
local parity = (x % 2 == 0) and "even" or "odd"
print(parity)   --> odd
```

Remember the pitfall: it fails when the "true" value is `false` or `nil`. In that case use an explicit `if`:

```lua
local flag = true
local value
if flag then
  value = false          -- the idiom `flag and false or "fallback"` would give "fallback"
else
  value = "fallback"
end
print(value)             --> false
```

The idiom is great for defaults and labels. For anything more complex, a plain `if` statement is clearer.

---

## 7.6 Simulating `switch` with Tables

Lua has no `switch` or `case`. When you would compare one value against many options, use a **table lookup**.

**Mapping values to values:**

```lua
local day_names = {
  [1] = "Monday", [2] = "Tuesday", [3] = "Wednesday",
  [4] = "Thursday", [5] = "Friday", [6] = "Saturday", [7] = "Sunday",
}
print(day_names[3] or "Unknown")   --> Wednesday
```

**Mapping values to actions** (functions):

```lua
local actions = {
  start = function() print("Starting...") end,
  stop  = function() print("Stopping...") end,
  pause = function() print("Pausing...") end,
}

local command = "stop"
local action = actions[command]
if action then
  action()
else
  print("Unknown command: " .. command)   -- the "default" case
end
--> Stopping...
```

**Several keys sharing one action** (like fall-through):

```lua
local function greet() print("Hello!") end
local handlers = { hi = greet, hello = greet, hey = greet }
handlers["hey"]()   --> Hello!
```

Advantages over long `if/elseif` chains: the table is data, so you can add or remove cases at run time, and lookup speed does not depend on the number of cases.

---

## 7.7 Guard Clauses and Early Returns

A **guard clause** checks for a bad or special case at the top of a function and leaves immediately with `return`. This keeps the main logic un-nested.

Nested version:

```lua
local function ship_order(order)
  if order then
    if order.paid then
      if #order.items > 0 then
        print("Shipping " .. #order.items .. " items")
      end
    end
  end
end
```

Guard-clause version:

```lua
local function ship_order(order)
  if not order then return end
  if not order.paid then return end
  if #order.items == 0 then return end

  print("Shipping " .. #order.items .. " items")
end

ship_order({ paid = true, items = { "pen", "book" } })   --> Shipping 2 items
ship_order(nil)                                          -- does nothing
```

Rules to remember:

- `return` must be the **last statement in a block**. If you need to return in the middle of a block (for example while debugging), wrap it: `do return end`.
- A function can `return nil, "reason"` to report why it bailed out (Lesson 12).
- Use `break` for the same purpose inside loops (Lesson 8).

```lua
local function debug_demo()
  print("before")
  do return end          -- legal: return is the last statement of the do-block
  print("after")         -- never reached
end
debug_demo()             --> before
```

---

[Previous](./[6]-Operators-And-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[8]-Loops.md)
