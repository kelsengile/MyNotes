[Previous](./[14]-Iterating-Over-Tables.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[16]-Metatables-And-Metamethods.md)

*Data Structures*

# Lesson 15 - Common Data Structures

Lua gives you one container, the table, but with it you can build every classic data structure. This lesson shows the most common ones, with complete working implementations.

---

## 15.1 Stacks

A **stack** is *last in, first out* (LIFO), like a pile of plates: you add and remove at the top. Use the end of an array as the top.

```lua
local Stack = {}
Stack.__index = Stack

function Stack.new()
  return setmetatable({ items = {}, size = 0 }, Stack)
end

function Stack:push(value)
  self.size = self.size + 1
  self.items[self.size] = value
end

function Stack:pop()
  if self.size == 0 then return nil end
  local value = self.items[self.size]
  self.items[self.size] = nil          -- free the slot
  self.size = self.size - 1
  return value
end

function Stack:peek()
  return self.items[self.size]
end

function Stack:is_empty()
  return self.size == 0
end

local s = Stack.new()
s:push("a"); s:push("b"); s:push("c")
print(s:pop(), s:pop())   --> c	b
print(s:peek())           --> a
```

For a quick stack you do not need a class; the `table` library is enough:

```lua
local stack = {}
table.insert(stack, 1)     -- push
table.insert(stack, 2)
print(table.remove(stack)) --> 2 (pop)
```

Typical uses: undo history, parsing nested brackets, depth-first search, evaluating expressions. Storing the size explicitly avoids depending on `#` and lets the stack hold `nil`-free values reliably.

---

## 15.2 Queues and Deques

A **queue** is *first in, first out* (FIFO), like a line of people. The naive approach `table.remove(q, 1)` shifts every element and is O(n). A better design keeps two indices, `first` and `last`, so both operations are O(1).

```lua
local Queue = {}
Queue.__index = Queue

function Queue.new()
  return setmetatable({ first = 1, last = 0, items = {} }, Queue)
end

function Queue:push(value)          -- enqueue at the back
  self.last = self.last + 1
  self.items[self.last] = value
end

function Queue:pop()                -- dequeue from the front
  if self.first > self.last then return nil end
  local value = self.items[self.first]
  self.items[self.first] = nil      -- allow garbage collection
  self.first = self.first + 1
  return value
end

function Queue:size()
  return self.last - self.first + 1
end

local q = Queue.new()
q:push("x"); q:push("y"); q:push("z")
print(q:pop(), q:pop(), q:size())   --> x	y	1
```

A **deque** (double-ended queue) supports adding and removing at both ends. The same two-index trick works because table keys can be zero or negative; `first` moves down when you push to the front:

```lua
local Deque = {}
Deque.__index = Deque

function Deque.new()
  return setmetatable({ first = 0, last = -1, items = {} }, Deque)
end

function Deque:push_front(v)
  self.first = self.first - 1
  self.items[self.first] = v
end

function Deque:push_back(v)
  self.last = self.last + 1
  self.items[self.last] = v
end

function Deque:pop_front()
  if self.first > self.last then return nil end
  local v = self.items[self.first]
  self.items[self.first] = nil
  self.first = self.first + 1
  return v
end

function Deque:pop_back()
  if self.first > self.last then return nil end
  local v = self.items[self.last]
  self.items[self.last] = nil
  self.last = self.last - 1
  return v
end

local d = Deque.new()
d:push_back(2); d:push_back(3); d:push_front(1)
print(d:pop_front(), d:pop_back())   --> 1	3
```

Because the indices are not kept within `1..n`, `#` and `ipairs` do not work on `items`; always go through the methods. Typical uses: task schedulers, breadth-first search, buffering.

---

## 15.3 Sets

A **set** holds unique values with no duplicates and no order. In Lua, use the values as **keys** and `true` as the stored value. Membership is then a fast lookup.

```lua
local fruits = { apple = true, banana = true }

print(fruits.apple)          --> true
print(fruits.cherry)         --> nil   (not a member)

fruits.cherry = true         -- add
fruits.apple = nil           -- remove

for item in pairs(fruits) do print(item) end   -- iterate (unordered)
```

A handy constructor from a list, plus set operations:

```lua
local function Set(list)
  local s = {}
  for _, v in ipairs(list) do s[v] = true end
  return s
end

local function union(a, b)
  local r = {}
  for k in pairs(a) do r[k] = true end
  for k in pairs(b) do r[k] = true end
  return r
end

local function intersection(a, b)
  local r = {}
  for k in pairs(a) do
    if b[k] then r[k] = true end
  end
  return r
end

local function to_sorted_list(s)
  local l = {}
  for k in pairs(s) do l[#l + 1] = k end
  table.sort(l)
  return l
end

local A, B = Set { 1, 2, 3 }, Set { 2, 3, 4 }
print(table.concat(to_sorted_list(union(A, B)), ","))          --> 1,2,3,4
print(table.concat(to_sorted_list(intersection(A, B)), ","))   --> 2,3
```

Removing duplicates from a list uses the same trick:

```lua
local function unique(list)
  local seen, result = {}, {}
  for _, v in ipairs(list) do
    if not seen[v] then
      seen[v] = true
      result[#result + 1] = v
    end
  end
  return result
end
print(table.concat(unique({ 3, 1, 3, 2, 1 }), ","))   --> 3,1,2
```

---

## 15.4 Linked Lists

A **linked list** is a chain of nodes; each node stores a value and a reference to the next node. Insertion and removal at a known position cost O(1), without shifting elements. Each node is a small table.

```lua
local list = nil                       -- empty list

local function prepend(list, value)
  return { value = value, next = list }
end

list = prepend(list, "c")
list = prepend(list, "b")
list = prepend(list, "a")              -- a -> b -> c

local node = list
while node do
  io.write(node.value, " ")
  node = node.next
end
print()   --> a b c
```

A slightly fuller version with a head and a tail pointer, enabling O(1) append:

```lua
local LinkedList = {}
LinkedList.__index = LinkedList

function LinkedList.new()
  return setmetatable({ head = nil, tail = nil, size = 0 }, LinkedList)
end

function LinkedList:append(value)
  local node = { value = value, next = nil }
  if self.tail then self.tail.next = node else self.head = node end
  self.tail = node
  self.size = self.size + 1
end

function LinkedList:remove_first()
  local node = self.head
  if not node then return nil end
  self.head = node.next
  if not self.head then self.tail = nil end
  self.size = self.size - 1
  return node.value
end

function LinkedList:iter()               -- iterator for use in a for loop
  local node = self.head
  return function()
    if node then
      local v = node.value
      node = node.next
      return v
    end
  end
end

local l = LinkedList.new()
l:append(10); l:append(20); l:append(30)
for v in l:iter() do io.write(v, " ") end
print()                                  --> 10 20 30
print(l:remove_first(), l.size)          --> 10	2
```

In everyday Lua, plain arrays are usually faster and simpler than linked lists. Choose a linked list when you frequently insert or remove in the middle and already hold a reference to the node.

---

## 15.5 Matrices and Grids

A **matrix** or **grid** is a table of tables, indexed `grid[row][column]` (see Lesson 13.10). A helper creates one with a default value:

```lua
local function new_grid(rows, cols, fill)
  local g = {}
  for r = 1, rows do
    g[r] = {}
    for c = 1, cols do g[r][c] = fill end
  end
  return g
end

local grid = new_grid(3, 3, ".")
grid[2][2] = "#"

for r = 1, 3 do
  print(table.concat(grid[r]))
end
--> ...
--> .#.
--> ...
```

Matrix transposition:

```lua
local function transpose(m)
  local t = {}
  for r = 1, #m do
    for c = 1, #m[r] do
      t[c] = t[c] or {}
      t[c][r] = m[r][c]
    end
  end
  return t
end

local m = { { 1, 2, 3 }, { 4, 5, 6 } }
local tm = transpose(m)
print(#tm, #tm[1], tm[3][2])   --> 3	2	6
```

Neighbors in a grid: loop over the offsets and check the bounds first.

```lua
local function neighbors(grid, r, c)
  local result = {}
  for dr = -1, 1 do
    for dc = -1, 1 do
      if not (dr == 0 and dc == 0) then
        local nr, nc = r + dr, c + dc
        if grid[nr] and grid[nr][nc] ~= nil then
          result[#result + 1] = grid[nr][nc]
        end
      end
    end
  end
  return result
end
print(#neighbors(grid, 1, 1), #neighbors(grid, 2, 2))   --> 3	8
```

A flat single array can also store a grid: index `(r - 1) * cols + c`. It uses less memory and is faster for big grids, at the cost of a bit of arithmetic.

---

## 15.6 Sparse Arrays

A **sparse array** has mostly empty positions, such as a huge map where only a few cells hold something. Storing the empty cells in a real array would waste memory, but since tables are hash maps, you can store **only the entries that exist**:

```lua
local sparse = {}
sparse[1] = "first"
sparse[1000000] = "millionth"
sparse[-5] = "negative index is fine"

print(sparse[1000000])   --> millionth
print(sparse[500])       --> nil   (empty cells cost nothing)
```

The table uses memory for just three entries. The tradeoff: `#sparse` and `ipairs` are meaningless here (the sequence ends at the first missing index), so iterate with `pairs`:

```lua
local count = 0
for index, value in pairs(sparse) do count = count + 1 end
print(count)   --> 3
```

A 2D sparse grid uses combined keys. A string key or a computed numeric key works:

```lua
local world = {}
local function key(x, y) return x .. "," .. y end   -- string key, e.g. "10,-3"

world[key(10, -3)] = "tree"
world[key(0, 0)] = "player"
print(world[key(10, -3)])    --> tree
print(world[key(5, 5)])      --> nil
```

Numeric keys avoid creating a string for every lookup: `y * 100000 + x` works if coordinates stay within a known range. For truly sparse matrices (such as polynomial coefficients), store only non-zero values and treat missing ones as zero:

```lua
local function get(m, i, j) return (m[i] and m[i][j]) or 0 end
local matrix = { [1] = { [1] = 5 }, [100] = { [100] = 7 } }
print(get(matrix, 1, 1), get(matrix, 2, 2))   --> 5	0
```

---

## 15.7 String Buffers (`table.concat`)

Strings are immutable, so `s = s .. piece` creates a **new** string every time and copies all previous characters. Doing that inside a loop becomes slow (quadratic time) and creates a lot of garbage for the collector.

```lua
-- Slow for large n
local s = ""
for i = 1, 10000 do
  s = s .. i .. ","
end
```

The fast alternative is a **string buffer**: collect pieces in a table, and join them once at the end with `table.concat`.

```lua
local buffer = {}
for i = 1, 10000 do
  buffer[#buffer + 1] = i          -- numbers are fine
  buffer[#buffer + 1] = ","
end
local result = table.concat(buffer)
print(#result)   --> 48894 (length of the joined text)
```

`table.concat(t, sep)` accepts a separator, so you often don't need to add commas yourself:

```lua
local parts = {}
for i = 1, 5 do parts[#parts + 1] = "item" .. i end
print(table.concat(parts, ", "))   --> item1, item2, item3, item4, item5
```

A small reusable buffer object:

```lua
local function StringBuilder()
  local parts = {}
  local sb = {}
  function sb.add(...)
    for i = 1, select("#", ...) do
      parts[#parts + 1] = tostring((select(i, ...)))
    end
    return sb                       -- allows chaining
  end
  function sb.build(sep) return table.concat(parts, sep) end
  return sb
end

local b = StringBuilder()
b.add("Hello", ", ").add("World", "!")
print(b.build())   --> Hello, World!
```

Rule of thumb: for a handful of concatenations, `..` is fine and clearer. In loops that run many times, or when assembling large text (reports, JSON, logs), use `table.concat`. Lesson 43 revisits this under performance.

---

[Previous](./[14]-Iterating-Over-Tables.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[16]-Metatables-And-Metamethods.md)
