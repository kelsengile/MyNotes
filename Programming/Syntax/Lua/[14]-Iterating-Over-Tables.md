[Previous](./[13]-Tables.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[15]-Common-Data-Structures.md)

*Data Structures*

# Lesson 14 - Iterating Over Tables

Once data is in a table, you need to walk through it. This lesson covers `ipairs`, `pairs`, and `next`, what order you can and cannot rely on, how to change a table safely while looping, and how to build your own iteration orders.

---

## 14.1 `ipairs` for Sequences

`ipairs(t)` walks the integer keys `1, 2, 3, ...` **in order** and stops at the first `nil`. It is the right tool for arrays.

```lua
local planets = { "Mercury", "Venus", "Earth" }

for i, name in ipairs(planets) do
  print(i, name)
end
--> 1 Mercury
--> 2 Venus
--> 3 Earth
```

Behaviors to know:

- **Order is guaranteed**: always ascending from 1.
- **It stops at the first hole**, so later elements are never visited:

```lua
local t = { "a", "b", nil, "d" }
for i, v in ipairs(t) do print(i, v) end
--> 1 a
--> 2 b          (stops here; "d" is skipped)
```

- Non-integer keys (like `name = "x"`) are ignored.
- You can name only the index or only the value: `for i in ipairs(t)` gives indices; `for _, v in ipairs(t)` gives values.

For a numeric `for` over `1..#t`, behavior is nearly identical on proper sequences:

```lua
for i = 1, #planets do print(planets[i]) end
```

The numeric loop is slightly faster and lets you control the step; `ipairs` reads more clearly and stops cleanly at the end of the sequence.

**Version note:** In Lua 5.3 and later, `ipairs` respects the `__index` metamethod; in 5.1/5.2 and LuaJIT it does not.

---

## 14.2 `pairs` for All Keys

`pairs(t)` visits **every** key-value pair: array entries, string keys, and everything else.

```lua
local mixed = { "x", "y", color = "red", size = 3 }

for key, value in pairs(mixed) do
  print(key, value)
end
-- Possible output (order may differ):
-- 1	x
-- 2	y
-- color	red
-- size	3
```

Typical uses:

```lua
-- Count entries
local n = 0
for _ in pairs(mixed) do n = n + 1 end
print(n)    --> 4

-- Collect all keys
local keys = {}
for k in pairs(mixed) do keys[#keys + 1] = tostring(k) end

-- Print a dictionary
local scores = { ann = 90, bo = 85 }
for name, score in pairs(scores) do
  print(name .. ": " .. score)
end
```

`pairs` also visits entries whose values are `false`, but never entries whose values are `nil` (since those do not exist).

The `__pairs` metamethod can customize what `pairs` does for a table (Lesson 16).

---

## 14.3 Iteration Order Guarantees

Lua makes limited promises about order:

| Iterator | Order guarantee |
|---|---|
| `ipairs(t)` | Always `1, 2, 3, ...` until the first `nil` |
| `pairs(t)` | **No guaranteed order** |
| numeric `for` | Exactly the order you specify |

With `pairs`, the traversal order for the same table can differ between runs, Lua versions, and platforms; string keys are hashed with a seed that can vary. Code must never depend on a particular order. In practice, array-style entries tend to come first, but even that is not promised.

```lua
local ages = { ann = 31, bo = 25, cy = 40 }
for name in pairs(ages) do io.write(name, " ") end
print()   -- e.g. "cy ann bo", but it may differ on another run
```

If you need a stable, predictable order, impose one:

- Keep an **array of keys** alongside the dictionary and walk that.
- **Sort the keys** before iterating (14.6).
- Use arrays (with `ipairs`) when order matters, and dictionaries when lookup matters.

This also matters for output: sorting keys before printing makes results reproducible and tests reliable.

---

## 14.4 `next` and Checking for Empty Tables

`pairs` is built on the primitive function **`next(t [, key])`**, which returns the entry that follows `key` in traversal order (or the first entry when `key` is `nil`), and returns `nil` when there are no more.

```lua
local t = { a = 1 }
print(next(t))        --> a	1
print(next(t, "a"))   --> nil
print(next({}))       --> nil
```

Because `next(t)` returns `nil` only when the table is empty, it is the standard emptiness test:

```lua
local function is_empty(t)
  return next(t) == nil
end

print(is_empty({}))           --> true
print(is_empty({ x = 1 }))    --> false
print(is_empty({ false }))    --> false  (false is a value; the entry exists)
```

Comparing `#t == 0` is **not** a reliable emptiness check: a table with only string keys has `#t == 0` but is not empty.

```lua
local d = { name = "Ada" }
print(#d == 0, next(d) == nil)   --> true	false
```

The generic `for` is exactly `next` in a loop. The call `pairs(t)` returns three values: `next, t, nil`, which the loop uses to repeat `next(t, previousKey)` (see Lesson 21).

---

## 14.5 Modifying a Table During Iteration

Changing a table while you traverse it with `pairs` or `next` is risky. The rules:

- **Allowed:** assigning `nil` to an **existing** field (including the current one), and changing the **value** of an existing field.
- **Undefined behavior:** **adding new keys** during traversal. It may work, skip entries, repeat entries, or raise an error ("invalid key to 'next'").

Safe removal during traversal:

```lua
local scores = { ann = 90, bo = 40, cy = 55, di = 70 }
for name, score in pairs(scores) do
  if score < 60 then
    scores[name] = nil      -- allowed: clearing an existing field
  end
end
-- remaining: ann, di
```

Unsafe: adding entries.

```lua
-- DON'T do this:
-- for k, v in pairs(t) do t[k .. "_copy"] = v end   -- undefined behavior
```

The safe approach is to collect changes first and apply them after the loop:

```lua
local t = { a = 1, b = 2 }
local additions = {}
for k, v in pairs(t) do
  additions[k .. "_copy"] = v
end
for k, v in pairs(additions) do t[k] = v end
```

**Arrays with `ipairs` or numeric loops** have their own trap: removing elements while walking forward shifts the later ones and makes you skip items. Walk backward instead:

```lua
local nums = { 1, 2, 3, 4, 5, 6 }
for i = #nums, 1, -1 do
  if nums[i] % 2 == 0 then
    table.remove(nums, i)
  end
end
print(table.concat(nums, ","))   --> 1,3,5
```

---

## 14.6 Reverse and Custom Iteration Orders

**Reverse order** for arrays uses a numeric `for` with a negative step:

```lua
local letters = { "a", "b", "c" }
for i = #letters, 1, -1 do
  io.write(letters[i], " ")
end
print()   --> c b a
```

**Sorted-key order** for dictionaries: collect keys, sort them, then loop.

```lua
local ages = { ann = 31, bo = 25, cy = 40 }

local keys = {}
for k in pairs(ages) do keys[#keys + 1] = k end
table.sort(keys)

for _, k in ipairs(keys) do
  print(k, ages[k])
end
--> ann 31 / bo 25 / cy 40
```

**Wrap that into a reusable iterator** that returns a closure (Lesson 21 explains the technique):

```lua
local function sorted_pairs(t, comp)
  local keys = {}
  for k in pairs(t) do keys[#keys + 1] = k end
  table.sort(keys, comp)
  local i = 0
  return function()
    i = i + 1
    local k = keys[i]
    if k ~= nil then return k, t[k] end
  end
end

for name, age in sorted_pairs(ages) do print(name, age) end
--> ann 31 / bo 25 / cy 40

-- sort by value, descending
local by_age = function(a, b) return ages[a] > ages[b] end
for name, age in sorted_pairs(ages, by_age) do print(name, age) end
--> cy 40 / ann 31 / bo 25
```

**Custom reverse iterator** for arrays:

```lua
local function reverse_ipairs(t)
  local i = #t + 1
  return function()
    i = i - 1
    if i >= 1 then return i, t[i] end
  end
end

for i, v in reverse_ipairs({ "x", "y", "z" }) do print(i, v) end
--> 3 z / 2 y / 1 x
```

Sorting only works when keys can be compared with each other. A table with both numeric and string keys needs a comparator that handles mixed types, for example by comparing `tostring(a) < tostring(b)`.

---

[Previous](./[13]-Tables.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[15]-Common-Data-Structures.md)
