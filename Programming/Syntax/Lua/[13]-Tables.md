[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[14]-Iterating-Over-Tables.md)

*Data Structures*

# Lesson 13 - Tables

Tables are Lua's only data structure, and they do the work of arrays, dictionaries, records, sets, and objects. Nearly everything else in the language (modules, classes, the global environment) is built on them, so this lesson is one of the most important in the course.

---

## 13.1 What is a Table?

A table is an **associative array**: a collection of key-value pairs where keys can be almost any value (numbers, strings, booleans, functions, other tables) and values can be anything.

```lua
local t = {}            -- create an empty table
t["name"] = "Ada"       -- string key
t[1] = "first"          -- number key
t[true] = "yes"         -- boolean key
print(t.name, t[1], t[true])   --> Ada	first	yes
```

Key facts:

- Tables are created with a **constructor** `{ }` and are always **anonymous**; variables just hold references to them.
- A key can be any value **except `nil`** (and `NaN`). Assigning `t[nil] = 1` raises an error.
- Reading a missing key returns `nil`, not an error.
- Float keys with integer values are normalized to integers, so `t[1.0]` and `t[1]` are the same slot.
- Tables grow automatically; there is no declared size.

Internally Lua stores consecutive integer keys in an efficient **array part** and everything else in a **hash part**, but you don't manage that yourself.

---

## 13.2 Table Constructors

The constructor `{ ... }` can fill a table immediately.

**List style** (values only; keys become 1, 2, 3, ...):

```lua
local fruits = { "apple", "banana", "cherry" }
print(fruits[1], fruits[3])   --> apple	cherry
```

**Record style** (`name = value`):

```lua
local person = { name = "Ada", age = 36 }
print(person.name, person.age)   --> Ada	36
```

**General style** (`[expression] = value`), needed for keys that are not valid names or are computed:

```lua
local key = "dynamic"
local t = {
  [1] = "one",
  ["two words"] = 2,
  [key] = true,
  [10 * 2] = "twenty",
}
print(t[1], t["two words"], t.dynamic, t[20])   --> one	2	true	twenty
```

Styles can be mixed, and fields can be separated by commas **or semicolons**. A trailing separator is allowed, which makes editing lists easier:

```lua
local mixed = {
  "first", "second";
  title = "Mixed",
}
```

**Function results expand at the end.** If the last item in a constructor is a function call returning several values, all of them are included. In any other position only the first value is kept:

```lua
local function three() return 1, 2, 3 end
local a = { three() }          -- {1, 2, 3}
local b = { three(), 10 }      -- {1, 10}
local c = { (three()) }        -- {1}
print(#a, #b, #c)              --> 3	2	1
```

---

## 13.3 Arrays and 1-Based Indexing

Lua has no separate array type. An **array** (or **sequence**) is a table whose keys are the integers `1, 2, 3, ...`. By convention Lua arrays are **1-based**: the first element is at index `1`, and the last is at `#t`.

```lua
local days = { "Mon", "Tue", "Wed" }
print(days[1])        --> Mon
print(days[#days])    --> Wed
print(days[0])        --> nil   (index 0 is just an empty key)

for i = 1, #days do
  print(i, days[i])
end
```

Appending and common operations:

```lua
local list = {}
list[#list + 1] = "a"       -- append (fast, idiomatic)
table.insert(list, "b")     -- also appends
print(#list, list[2])       --> 2	b
```

Why 1-based? It's a design choice rooted in Lua's origin as a data-description language; it also means the length equals the last index. Be careful when porting algorithms from 0-based languages: an off-by-one error is the most common mistake. Nothing stops you from using index `0` or negative indices, but library functions (`ipairs`, `#`, `table.insert`, `table.sort`) ignore them.

---

## 13.4 Dictionaries and Record-Style Access (`t.key` vs `t["key"]`)

A table with string keys works like a dictionary or a record. Two syntaxes read and write the same entry:

```lua
local user = {}
user.name = "Ada"          -- dot syntax
user["name"] = "Ada"       -- bracket syntax (identical)
print(user.name)           --> Ada
```

`t.key` is just sugar for `t["key"]`, and works only when the key is a valid identifier (letters, digits, underscores, not starting with a digit, and not a reserved word). Use brackets when the key:

- contains spaces or symbols: `t["first name"]`,
- is a reserved word: `t["end"]`,
- is stored in a variable (computed at run time):

```lua
local field = "name"
print(user[field])    --> Ada     (looks up the key "name")
print(user.field)     --> nil     (looks up the literal key "field"!)
```

Mixing up `t.x` and `t[x]` is a classic mistake: `t.x` uses the **text** `"x"`, while `t[x]` uses the **value** of the variable `x`.

Number keys and string keys are different: `t[1]` and `t["1"]` are two separate entries.

```lua
local t = {}
t[1] = "number"
t["1"] = "string"
print(t[1], t["1"])   --> number	string
```

---

## 13.5 Mixed Tables

One table can hold both array-style and record-style entries:

```lua
local inventory = {
  "sword", "shield", "potion",      -- array part: [1], [2], [3]
  gold = 120,                        -- record part
  owner = "Ada",
}

print(#inventory)        --> 3     (# counts only the sequence)
print(inventory.gold)    --> 120
for i, item in ipairs(inventory) do print(i, item) end   -- only 1..3
```

`#` and `ipairs` look only at the sequence of integer keys starting at 1, ignoring `gold` and `owner`. `pairs` visits everything (Lesson 14).

Mixed tables are natural for objects that also hold a list (for example, a `Team` with a name and members), but if the data is conceptually two things, nested tables are often clearer:

```lua
local team = { name = "Red", members = { "Ann", "Bo" } }
```

---

## 13.6 Adding, Updating, and Removing Items

**Add or update** by assignment; Lua does not distinguish between the two:

```lua
local t = { a = 1 }
t.b = 2          -- add
t.a = 10         -- update
t[#t + 1] = "x"  -- append to the array part
```

**Remove** by assigning `nil`. The entry disappears:

```lua
t.b = nil
print(t.b)       --> nil
```

For arrays, setting an element to `nil` leaves a **hole** rather than shifting the other elements. To remove from the middle and close the gap, use `table.remove` (13.8):

```lua
local arr = { "a", "b", "c", "d" }
arr[2] = nil               -- hole! {"a", nil, "c", "d"}
-- better:
local arr2 = { "a", "b", "c", "d" }
table.remove(arr2, 2)      -- {"a", "c", "d"}
print(#arr2)               --> 3
```

**Check whether a key exists** by comparing with `nil` (a stored value of `false` still counts as existing, so be careful not to use plain truthiness):

```lua
local flags = { debug = false }
print(flags.debug)            --> false
print(flags.debug ~= nil)     --> true   (key exists)
print(flags.verbose ~= nil)   --> false  (key missing)
```

**Clear a table** by looping and setting each key to `nil` (assigning existing fields to `nil` during `pairs` traversal is safe):

```lua
for k in pairs(t) do t[k] = nil end
```

---

## 13.7 The Length Operator and Holes (`nil` in Arrays)

`#t` returns a **border**: an index `n` such that `t[n] ~= nil` and `t[n+1] == nil` (or 0 for an empty table). For a proper sequence with no holes there is exactly one border, and `#t` is the number of elements:

```lua
print(#{ 1, 2, 3 })        --> 3
print(#{})                 --> 0
```

If the table has **holes** (`nil` values inside), there can be several borders, and `#` may return any of them. The result can differ between Lua versions and even between runs of different code:

```lua
local holey = { 1, 2, nil, 4 }
print(#holey)              --> 4 (in many builds; could also be 2)
local holey2 = { 1, 2 }
holey2[4] = 4
print(#holey2)             --> 2 (or 4: don't rely on either)
```

Guidelines:

- Never put `nil` inside an array you plan to measure with `#` or traverse with `ipairs`.
- Track the count yourself when `nil` values are meaningful, e.g. `{ n = 4, 1, 2, nil, 4 }`; `table.pack(...)` does this for you.
- To represent "empty slot" inside an array, use a sentinel value such as `false` or a special table instead of `nil`.
- To get the true number of entries in a table with arbitrary keys, count them with `pairs`:

```lua
local function count(t)
  local n = 0
  for _ in pairs(t) do n = n + 1 end
  return n
end
print(count({ a = 1, b = 2, 10 }))   --> 3
```

---

## 13.8 The `table` Library (`insert`, `remove`, `concat`, `sort`, `unpack`, `move`)

**`table.insert(t, [pos,] value)`** appends, or inserts at `pos` and shifts later elements up:

```lua
local t = { "a", "c" }
table.insert(t, "d")        -- {"a","c","d"}
table.insert(t, 2, "b")     -- {"a","b","c","d"}
print(table.concat(t, ","))   --> a,b,c,d
```

**`table.remove(t [, pos])`** removes and **returns** the element at `pos` (default: the last), shifting later ones down:

```lua
print(table.remove(t))      --> d      (last)
print(table.remove(t, 1))   --> a      (first; others shift down)
print(table.concat(t, ","))   --> b,c
print(table.remove({}))     --> nil    (empty table is fine)
```

Removing from the front or inserting at the front is O(n); for queues see Lesson 15.

**`table.concat(t [, sep [, i [, j]]])`** joins elements into a string. Elements must be strings or numbers:

```lua
print(table.concat({ 1, 2, 3 }, "-"))          --> 1-2-3
print(table.concat({ "a", "b", "c" }, ", ", 2, 3))  --> b, c
```

**`table.sort(t [, comp])`** sorts **in place** (details in 13.9):

```lua
local nums = { 5, 2, 8, 1 }
table.sort(nums)
print(table.concat(nums, " "))   --> 1 2 5 8
```

**`table.unpack(t [, i [, j]])`** returns elements as separate values (global `unpack` in Lua 5.1):

```lua
local a, b, c = table.unpack({ 10, 20, 30 })
print(a, b, c)   --> 10	20	30
```

**`table.pack(...)`** returns a table with the arguments and a field `n` with the count.

**`table.move(a1, f, e, t [, a2])`** (Lua 5.3+) copies elements `a1[f..e]` to positions starting at `t` in `a2` (default `a1`). It handles overlapping ranges correctly:

```lua
local src = { 1, 2, 3, 4, 5 }
local dst = table.move(src, 2, 4, 1, {})     -- copy src[2..4] to new table at 1
print(table.concat(dst, ","))                --> 2,3,4

table.move(src, 1, 3, 3)                     -- shift within the same table
print(table.concat(src, ","))                --> 1,2,1,2,3
```

---

## 13.9 Sorting with Custom Comparators

By default `table.sort` orders elements with `<`, so it works on a list of numbers or a list of strings (but not a mix). For other orderings, pass a **comparator**: a function `comp(a, b)` that returns `true` if `a` should come **before** `b`.

```lua
local nums = { 5, 2, 8, 1 }
table.sort(nums, function(a, b) return a > b end)    -- descending
print(table.concat(nums, " "))   --> 8 5 2 1
```

Sorting records by a field:

```lua
local people = {
  { name = "Cy",  age = 25 },
  { name = "Ann", age = 31 },
  { name = "Bo",  age = 25 },
}

table.sort(people, function(a, b)
  if a.age ~= b.age then
    return a.age < b.age         -- primary key: age ascending
  end
  return a.name < b.name         -- tie-breaker: name ascending
end)

for _, p in ipairs(people) do print(p.name, p.age) end
--> Bo 25 / Cy 25 / Ann 31
```

Rules for comparators:

- Return `true` only when `a` is **strictly** before `b`. Using `<=` can cause an error ("invalid order function for sorting") or inconsistent results.
- The comparator must be consistent: if `comp(a,b)` is true then `comp(b,a)` must be false.
- `table.sort` is **not stable**: equal elements may be reordered. Add a tie-breaker (as above) when order of equals matters.
- Sorting needs a proper sequence without holes, and elements must be comparable (sorting `{1, "a"}` raises a comparison error).
- Sort only sorts the array part. To sort the keys of a dictionary, copy them into an array first (Lesson 14).

---

## 13.10 Nested Tables and Multidimensional Arrays

Tables can hold tables, which gives you records of records and multidimensional arrays.

```lua
local company = {
  name = "Acme",
  address = { city = "Paris", zip = "75001" },
  staff = {
    { name = "Ann", role = "dev" },
    { name = "Bo",  role = "ops" },
  },
}
print(company.address.city)        --> Paris
print(company.staff[2].name)       --> Bo
```

A **2D grid** is an array of arrays. Create each row separately, because `{ {}, {}, {} }` written with the same table repeated would share rows:

```lua
local rows, cols = 3, 4
local grid = {}
for r = 1, rows do
  grid[r] = {}                     -- a new row table each time
  for c = 1, cols do
    grid[r][c] = (r - 1) * cols + c
  end
end
print(grid[2][3])                  --> 7
```

The classic bug: filling with one shared row.

```lua
local row = { 0, 0, 0 }
local bad = { row, row, row }      -- three references to the SAME table
bad[1][1] = 9
print(bad[2][1])                   --> 9   (all rows changed)
```

Accessing a nested key when an intermediate table may be missing raises an error. Guard with `and`:

```lua
local city = company.address and company.address.city
local x = company.contact and company.contact.phone   -- nil, no error
```

---

## 13.11 Copying Tables: Shallow vs Deep

Assigning a table to another variable does **not** copy it; both names refer to the same table (13.12). To get an independent copy you must build one.

**Shallow copy** duplicates the top level only. Nested tables are still shared:

```lua
local function shallow_copy(t)
  local copy = {}
  for k, v in pairs(t) do copy[k] = v end
  return copy
end

local original = { 1, 2, inner = { x = 1 } }
local copy = shallow_copy(original)
copy[1] = 99
copy.inner.x = 42
print(original[1])        --> 1    (separate top-level value)
print(original.inner.x)   --> 42   (nested table is shared!)
```

**Deep copy** duplicates nested tables recursively. A robust version also handles **cycles** (a table that contains itself) by remembering what it has already copied:

```lua
local function deep_copy(value, seen)
  if type(value) ~= "table" then return value end
  seen = seen or {}
  if seen[value] then return seen[value] end   -- already copied (cycle or repeat)

  local copy = {}
  seen[value] = copy
  for k, v in pairs(value) do
    copy[deep_copy(k, seen)] = deep_copy(v, seen)
  end
  return setmetatable(copy, getmetatable(value))
end

local d = deep_copy(original)
d.inner.x = 7
print(original.inner.x)   --> 42   (unchanged)
```

Notes: the deep copy above keeps the original's metatable (shared, not copied). Functions, coroutines, and userdata are copied by reference, because they cannot be duplicated in plain Lua. Choose shallow copies when sharing nested data is fine; they are cheaper.

---

## 13.12 Tables as References

A table variable holds a **reference** (a pointer-like handle) to the table, not the table itself. Assignment copies the reference. Passing a table to a function passes the reference too.

```lua
local a = { count = 1 }
local b = a              -- b refers to the same table
b.count = 2
print(a.count)           --> 2

local function bump(t) t.count = t.count + 1 end
bump(a)
print(b.count)           --> 3
```

Consequences:

- **Equality compares identity.** Two tables with identical contents are not equal (`{} == {}` is `false`).
- **Tables as keys** are matched by identity too, which is useful for associating data with objects.
- **Garbage collection:** a table is freed automatically when no references to it remain. Setting a variable to `nil` removes one reference:

```lua
local big = { 1, 2, 3 }
local alias = big
big = nil               -- table still alive: alias refers to it
alias = nil             -- now unreachable; the collector may free it
```

- **Default arguments trap:** never use a table constructor as a shared default, since all callers would share it. Create a new table inside the function.

```lua
local function add_item(item, list)
  list = list or {}      -- new table per call when none is given
  list[#list + 1] = item
  return list
end
```

---

[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[14]-Iterating-Over-Tables.md)
