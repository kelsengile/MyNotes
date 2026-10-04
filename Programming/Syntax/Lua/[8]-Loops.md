[Previous](./[7]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[9]-Functions.md)

*Core Syntax*

# Lesson 8 - Loops: while, repeat, for

Loops repeat code. Lua has three loop statements: `while`, `repeat ... until`, and `for` (in a numeric and a generic form). This lesson also covers how to leave a loop early, how to skip an iteration with `goto`, and how loop variables are scoped.

---

## 8.1 The `while` Loop

A `while` loop checks its condition **before** each pass and keeps going while the condition is truthy:

```lua
local n = 1
while n <= 5 do
  print(n)
  n = n + 1
end
--> 1 2 3 4 5 (each on its own line)
```

If the condition is false from the start, the body never runs. Make sure something in the body eventually makes the condition false, otherwise you get an infinite loop (stop it with `Ctrl+C`).

A deliberate infinite loop is common in game loops and servers:

```lua
while true do
  -- do work; use break to leave
  break
end
```

A practical example: halve a number until it is small.

```lua
local x = 100
local steps = 0
while x > 1 do
  x = x / 2
  steps = steps + 1
end
print(steps)   --> 7
```

---

## 8.2 The `repeat ... until` Loop

`repeat ... until` checks its condition **after** each pass, so the body always runs **at least once**. The loop stops when the condition becomes **true** (the opposite sense from `while`):

```lua
local tries = 0
repeat
  tries = tries + 1
  print("Attempt " .. tries)
until tries >= 3
--> Attempt 1, Attempt 2, Attempt 3
```

A special feature: the `until` expression can see **local variables declared inside the loop body**:

```lua
local i = 0
repeat
  i = i + 1
  local done = (i * i > 20)   -- local to the loop body
until done                    -- still visible here
print(i)   --> 5
```

Use `repeat` when you need the body to run once before testing, such as asking for input until it is valid.

---

## 8.3 The Numeric `for` Loop

The numeric `for` counts through a range:

```lua
for i = start, stop, step do
  -- body
end
```

`step` is optional and defaults to `1`.

```lua
for i = 1, 5 do
  io.write(i, " ")
end
print()   --> 1 2 3 4 5

for i = 10, 1, -3 do     -- counting down
  io.write(i, " ")
end
print()   --> 10 7 4 1

for i = 0, 1, 0.25 do    -- fractional steps
  io.write(i, " ")
end
print()   --> 0.0 0.25 0.5 0.75 1.0
```

Rules and gotchas:

- `start`, `stop`, and `step` are evaluated **once**, before the loop begins. Changing the variable that held `stop` inside the loop has no effect.
- The loop variable `i` is a **fresh local** for each pass. Assigning to it inside the body changes only that pass, not the loop's counting. (Avoid modifying it anyway.)
- If `step` is positive and `start > stop`, the body never runs. For a countdown you need a negative step.
- `step` cannot be zero (error: `'for' step is zero`).
- If `start` and `step` are integers, the loop variable stays an integer; otherwise it is a float. Lua 5.4 computes the iteration count up front for integer loops, so they never overflow or wrap around.
- Accumulated floating-point error can affect loops with fractional steps, so prefer integer counters when precision matters:

```lua
for k = 0, 10 do
  local x = k / 10
  io.write(x, " ")
end
print()   --> 0.0 0.1 0.2 0.3 0.4 0.5 0.6 0.7 0.8 0.9 1.0
```

The classic way to walk an array by index:

```lua
local fruits = { "apple", "banana", "cherry" }
for i = 1, #fruits do
  print(i, fruits[i])
end
```

---

## 8.4 The Generic `for` Loop (`pairs`, `ipairs`)

The generic `for` walks over values produced by an **iterator function**. The two most common iterators visit table contents.

**`ipairs(t)`** visits array elements `t[1]`, `t[2]`, ... in order and stops at the first `nil`:

```lua
local colors = { "red", "green", "blue" }
for index, color in ipairs(colors) do
  print(index, color)
end
--> 1 red / 2 green / 3 blue
```

**`pairs(t)`** visits **every** key-value pair, in no guaranteed order:

```lua
local person = { name = "Ada", age = 36, city = "London" }
for key, value in pairs(person) do
  print(key, value)
end
-- prints the three pairs in an unspecified order
```

| Use | When |
|---|---|
| `ipairs` | Array-like lists where order matters |
| `pairs` | Dictionaries, or tables where you want every key |
| numeric `for` | When you need index arithmetic or reverse/step control |

Ignore a value you do not need with the conventional name `_`:

```lua
for _, color in ipairs(colors) do print(color) end
for key in pairs(person) do print(key) end    -- just the keys
```

Lesson 14 digs into iteration order and safe modification, and Lesson 21 shows how to write your own iterators.

**Version note:** In Lua 5.1 and LuaJIT, `ipairs` stops at the first `nil` and ignores metamethods; in 5.3 and later it respects `__index`.

---

## 8.5 `break`

`break` exits the **innermost** enclosing loop immediately:

```lua
for i = 1, 10 do
  if i * i > 30 then
    print("stopping at", i)
    break
  end
end
--> stopping at 6
```

A typical use is searching:

```lua
local names = { "Ann", "Bob", "Cy", "Di" }
local found
for i, name in ipairs(names) do
  if name == "Cy" then
    found = i
    break
  end
end
print(found)   --> 3
```

In Lua 5.1, `break` had to be the last statement in a block. Since 5.2 it may appear anywhere, because it is syntactic sugar for `goto` to a label just after the loop.

---

## 8.6 `goto` and Simulating `continue`

Lua has no `continue` statement. To skip to the next iteration, use `goto` with a label placed at the end of the loop body.

A **label** is written `::name::`. `goto name` jumps to it.

```lua
for i = 1, 6 do
  if i % 2 == 0 then
    goto continue      -- skip even numbers
  end
  print(i)
  ::continue::
end
--> 1 3 5
```

Rules for `goto`:

- A label is visible in the entire block where it is defined, including nested blocks, but **not** inside nested *functions*.
- You cannot jump **into** the scope of a local variable. A label at the very end of a block is allowed even when locals were declared above it, because the locals are considered out of scope there.
- You cannot jump into a nested block, only out of one or within the same block.
- A `goto` cannot jump to a label in an enclosing function.

In `repeat ... until` loops, the `until` condition can see the body's locals, so a label placed right before `until` can fail with an error such as "jumps into the scope of local" if a local was declared after the `goto`. Avoid `goto continue` in `repeat` loops that declare locals, or wrap the body in `do ... end`.

```lua
local i = 0
repeat
  i = i + 1
  do
    if i == 2 then goto continue end
    print(i)
  end
  ::continue::
until i >= 4
--> 1 3 4
```

**Version note:** `goto` exists in Lua 5.2 and later (and LuaJIT 2.0+). It does not exist in plain Lua 5.1.

---

## 8.7 Nested Loops and Labels

Loops can be nested, with the inner loop running completely for every pass of the outer one:

```lua
for row = 1, 3 do
  for col = 1, 3 do
    io.write(row * col, "\t")
  end
  print()
end
--> 1 2 3
--> 2 4 6
--> 3 6 9
```

`break` leaves only the inner loop. To leave **several** loops at once, use `goto` with a label after the outer loop:

```lua
for i = 1, 5 do
  for j = 1, 5 do
    if i * j == 12 then
      print("found", i, j)
      goto done
    end
  end
end
::done::
print("finished")
--> found 3 4
--> finished
```

Alternatives are to move the nested loops into a function and `return` from it, or to use a flag variable that the outer loop checks. Returning from a function is often the cleanest option.

```lua
local function find_pair(target)
  for i = 1, 5 do
    for j = 1, 5 do
      if i * j == target then return i, j end
    end
  end
end
print(find_pair(12))   --> 3	4
```

---

## 8.8 Loop Variable Scope

Variables declared in the `for` header exist only inside the loop:

```lua
for i = 1, 3 do
  local squared = i * i     -- new local each pass
  print(i, squared)
end
print(i)   --> nil (i is not visible here; this reads a global named i)
```

A `local` declared inside the body is created fresh on every pass, which is what makes closures capture a different variable per iteration (Lesson 10.7).

If you need the final value after the loop, copy it to an outer variable:

```lua
local last
for i = 1, 5 do
  last = i
end
print(last)   --> 5
```

For a `while` loop, variables declared **before** the loop persist, while those declared **inside** the body are recreated each pass.

```lua
local total = 0           -- outside: persists
for i = 1, 4 do
  local doubled = i * 2   -- inside: new each pass
  total = total + doubled
end
print(total)   --> 20
```

---

[Previous](./[7]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[9]-Functions.md)
