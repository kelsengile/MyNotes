[Previous](./[10]-Scope-And-Closures.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[12]-Error-Handling.md)

*Core Syntax*

# Lesson 11 - String Library & Pattern Matching

Strings are used everywhere: text, file contents, commands, and messages. Lua's `string` library provides the tools to measure, slice, format, search, and rewrite them. Lua has its own **pattern** language rather than full regular expressions, which is smaller but surprisingly capable.

---

## 11.1 Basic Methods (`len`, `upper`, `lower`, `sub`, `rep`, `reverse`)

```lua
local s = "Hello, Lua"

print(string.len(s))        --> 10
print(string.upper(s))      --> HELLO, LUA
print(string.lower(s))      --> hello, lua
print(string.reverse(s))    --> auL ,olleH
print(string.rep("ab", 3))  --> ababab
print(string.rep("ab", 3, "-"))  --> ab-ab-ab   (optional separator, Lua 5.2+)
```

**`string.sub(s, i [, j])`** extracts a substring from position `i` to `j` (inclusive). Positions start at **1**, and **negative** positions count from the end (`-1` is the last character):

```lua
local t = "abcdef"
print(t:sub(2, 4))    --> bcd
print(t:sub(3))       --> cdef   (to the end)
print(t:sub(-3))      --> def    (last three)
print(t:sub(2, -2))   --> bcde
print(t:sub(10))      --> (empty string; out-of-range is not an error)
print(t:sub(0, 2))    --> ab     (0 is treated as 1)
```

Note that `#s` and `string.len(s)` count **bytes**, not characters. All these functions are byte-oriented and do not understand UTF-8 (see Lesson 32).

Because strings are immutable, none of these change the original; each returns a new string.

---

## 11.2 `string.format()` (and `%d`, `%s`, `%f`, `%q`)

`string.format(fmt, ...)` builds a string from a template, in the style of C's `printf`. Each `%` specifier is replaced by an argument:

| Specifier | Meaning | Example | Result |
|---|---|---|---|
| `%d` | Integer | `format("%d", 42)` | `42` |
| `%5d` | Integer padded to width 5 | `format("%5d", 42)` | `   42` |
| `%05d` | Zero-padded | `format("%05d", 42)` | `00042` |
| `%f` | Float (6 decimals) | `format("%f", 3.14159)` | `3.141590` |
| `%.2f` | Float with 2 decimals | `format("%.2f", 3.14159)` | `3.14` |
| `%e` | Scientific notation | `format("%.2e", 12345)` | `1.23e+04` |
| `%g` | Shortest of `%f`/`%e` | `format("%g", 0.5)` | `0.5` |
| `%s` | String (uses `tostring`) | `format("%s", true)` | `true` |
| `%10s` / `%-10s` | Right / left aligned | `format("%-6s\|", "ab")` | `ab    \|` |
| `%x` / `%X` | Hexadecimal | `format("%x", 255)` | `ff` |
| `%o` | Octal | `format("%o", 8)` | `10` |
| `%c` | Character from code | `format("%c", 65)` | `A` |
| `%q` | Quoted string, safe to read back by Lua | `format("%q", 'a "b"')` | `"a \"b\""` |
| `%%` | A literal percent sign | `format("%d%%", 50)` | `50%` |

```lua
local name, score = "Ada", 93.456
print(string.format("%s scored %.1f points (%d%%)", name, score, 93))
--> Ada scored 93.5 points (93%)

print(("%-8s|%6.2f|"):format("Total", 12.5))
--> Total   | 12.50|
```

Important details:

- `%d` requires a value with an **integer representation**. `string.format("%d", 3.0)` works, but `string.format("%d", 3.5)` raises an error ("number has no integer representation").
- `%s` converts any value with `tostring` (and respects `__tostring`), so it never fails on tables or `nil`.
- `%q` produces output that `load` can read back as the same value. Lesson 31 uses it for serialization.

---

## 11.3 Finding Text (`find`, `match`)

**`string.find(s, pattern [, init [, plain]])`** returns the **start and end positions** of the first match, or `nil`:

```lua
local text = "The quick brown fox"
print(string.find(text, "quick"))     --> 5	9
print(string.find(text, "slow"))      --> nil
print(string.find(text, "o"))         --> 13	13
print(string.find(text, "o", 14))     --> 18	18   (start searching at position 14)
```

By default the search string is a **pattern**, so characters like `.`, `%`, `-`, and `+` have special meanings. To search for literal text, pass `true` as the fourth argument ("plain"):

```lua
print(string.find("1+1=2", "1+1", 1, true))   --> 1	3
print(string.find("a.b", ".", 1, true))       --> 2	2   (only the real dot)
```

**`string.match(s, pattern [, init])`** returns the matched text itself, or the **captures** (parts in parentheses):

```lua
print(string.match("hello 123 world", "%d+"))          --> 123
print(string.match("key=value", "(%w+)=(%w+)"))        --> key	value
print(string.match("no digits", "%d+"))                --> nil
```

Use `find` when you want positions or just a yes/no answer; use `match` when you want the text.

```lua
if ("user@example.com"):find("@", 1, true) then
  print("looks like an email")
end
```

---

## 11.4 Iterating with `gmatch`

**`string.gmatch(s, pattern)`** returns an iterator that yields every match in turn, ideal for `for` loops:

```lua
for word in string.gmatch("one two  three", "%a+") do
  print(word)
end
--> one / two / three
```

With captures, each iteration yields all captures:

```lua
local config = "a=1, b=2, c=3"
for key, value in config:gmatch("(%w+)=(%w+)") do
  print(key, value)
end
--> a 1 / b 2 / c 3
```

Building a table from matches:

```lua
local numbers = {}
for n in ("10 20 30"):gmatch("%d+") do
  numbers[#numbers + 1] = tonumber(n)
end
print(#numbers, numbers[3])   --> 3	30
```

**Anchors do not work in `gmatch`.** A `^` at the start of a pattern is not treated as an anchor there (it would stop the iteration from advancing), so avoid it. Lua 5.4 also added an optional third argument, `init`, that says where to start searching.

---

## 11.5 Replacing with `gsub`

**`string.gsub(s, pattern, repl [, n])`** returns a new string with matches replaced, **and** the number of replacements made. The optional `n` limits how many replacements happen.

```lua
local s, count = string.gsub("hello world", "o", "0")
print(s, count)    --> hell0 w0rld	2

print(("aaa"):gsub("a", "b", 2))    --> bba	2
```

The replacement `repl` can be one of three things:

**1. A string**, where `%1`, `%2`, ... refer to captures and `%0` to the whole match (and `%%` is a literal percent):

```lua
print(("John Smith"):gsub("(%w+) (%w+)", "%2, %1"))   --> Smith, John	1
print(("price: 5"):gsub("%d+", "$%0"))                --> price: $5	1
```

**2. A table**, where the matched text (or first capture) is looked up as a key; if the value is `nil` or `false`, the match is left unchanged:

```lua
local vars = { name = "Ada", lang = "Lua" }
print(("$name loves $lang"):gsub("%$(%w+)", vars))
--> Ada loves Lua	2
```

**3. A function**, called with the captures; whatever it returns replaces the match (return `nil`/`false` to keep the original):

```lua
print(("1 2 3"):gsub("%d", function(d) return tonumber(d) * 2 end))
--> 2 4 6	3
```

If you only want the string and not the count, wrap the call in parentheses: `(s:gsub(...))`, or assign to one variable.

---

## 11.6 Lua Patterns (Character Classes, Anchors, Quantifiers, Captures)

A **pattern** describes text to match. Most characters match themselves; the following are special.

**Character classes** (an uppercase letter means the *complement*):

| Class | Matches | Complement |
|---|---|---|
| `.` | Any character | |
| `%a` | Letters | `%A` |
| `%d` | Digits | `%D` |
| `%l` | Lowercase letters | `%L` |
| `%u` | Uppercase letters | `%U` |
| `%s` | Whitespace | `%S` |
| `%w` | Letters and digits | `%W` |
| `%p` | Punctuation | `%P` |
| `%x` | Hexadecimal digits | `%X` |
| `%c` | Control characters | `%C` |

**Sets:** `[abc]` matches any of a, b, c; `[a-z]` is a range; `[^0-9]` is anything but a digit; classes work inside sets: `[%w_]`.

**Quantifiers** (apply to the single character, class, or set before them):

| Symbol | Meaning |
|---|---|
| `*` | 0 or more (greedy: as many as possible) |
| `+` | 1 or more (greedy) |
| `-` | 0 or more (**lazy**: as few as possible) |
| `?` | 0 or 1 |

**Anchors:** `^` at the start of a pattern matches only at the beginning of the string; `$` at the end matches only at the end.

**Captures:** parentheses save the matched part and return it. An empty capture `()` returns the **position**.

**Escaping:** the magic characters are `^ $ ( ) % . [ ] * + - ?`. Put `%` before one to match it literally, e.g. `%.` is a real dot and `%%` a percent sign.

**Extras:** `%b()` matches balanced parentheses (any pair of delimiters can be used, such as `%bxy`), `%f[set]` is the "frontier" that matches a transition into the set, and `%1` inside a pattern repeats the first capture.

```lua
print(("2024-05-17"):match("^(%d+)-(%d+)-(%d+)$"))   --> 2024	05	17
print(("  padded  "):match("^%s*(.-)%s*$"))           --> padded
print(("f(a(b)c)d"):match("%b()"))                   --> (a(b)c)
print(("THE (quick) fox"):find("%((%a+)%)"))         --> 5	11	quick
print(("hello"):find("()ll()"))                      --> 3	4	3	5
print(("say 'hi' or \"bye\""):match("(['\"])(.-)%1"))--> '	hi
print(("THE END"):gsub("%f[%a]%a+", string.lower))   --> the end	2
```

Greedy vs. lazy:

```lua
local html = "<b>bold</b> and <i>italic</i>"
print(html:match("<(.*)>"))    --> b>bold</b> and <i>italic</i   (greedy)
print(html:match("<(.-)>"))    --> b                             (lazy)
```

---

## 11.7 Lua Patterns vs Regular Expressions

Lua patterns look similar to regular expressions (regex) but are intentionally simpler. They are only a few hundred lines of C, which keeps Lua small. Know the differences:

| Feature | Regex (PCRE, JS, Python) | Lua patterns |
|---|---|---|
| Escape character | `\` | `%` |
| Alternation `a\|b` | Yes | **No** |
| Quantifier on a group `(ab)+` | Yes | **No**: quantifiers apply only to single characters, classes, or sets |
| Lazy quantifiers | `*?`, `+?` | `-` |
| `{n,m}` repetition | Yes | **No** |
| Lookahead / lookbehind | Yes | **No** (use `%f` frontier for some cases) |
| Non-capturing groups | `(?:...)` | No |
| Named captures | Yes | No |
| Balanced delimiters | Hard | `%b()` |
| `\d`, `\w`, `\s` | Yes | `%d`, `%w`, `%s` |
| Case-insensitive flag | Yes | No (write `[Hh]` or lowercase first) |

Workarounds:

- **Alternation:** try each pattern in turn, or match each alternative separately:

```lua
local function is_pet(s)
  return s:find("^cat") ~= nil or s:find("^dog") ~= nil   -- "cat|dog" at the start
end
print(is_pet("catfish"), is_pet("bird"))   --> true	false
```

- **Case-insensitive search:** convert both sides: `s:lower():find(word:lower(), 1, true)`.
- For truly regex-heavy tasks, use a library such as **LPeg** (a powerful parsing library) or a PCRE binding from LuaRocks (Lesson 3).

---

## 11.8 `string.byte` and `string.char`

Each character in a Lua string is a byte, with a numeric code from 0 to 255.

**`string.byte(s [, i [, j]])`** returns the codes of characters `i` through `j` (default: first character only):

```lua
print(string.byte("A"))          --> 65
print(string.byte("ABC", 1, 3))  --> 65	66	67
print(("abc"):byte(-1))          --> 99   (last character)
```

**`string.char(...)`** builds a string from codes:

```lua
print(string.char(72, 105))      --> Hi
print(string.char(0x4C, 0x75, 0x61))  --> Lua
```

Practical uses:

```lua
-- Caesar cipher (shift letters by 3)
local function caesar(s, shift)
  return (s:gsub("%a", function(c)
    local base = c:match("%l") and 97 or 65
    return string.char((c:byte() - base + shift) % 26 + base)
  end))
end
print(caesar("Hello, World", 3))   --> Khoor, Zruog

-- Check if a character is a digit by code
local c = ("7"):byte()
print(c >= 48 and c <= 57)         --> true
```

Because these work on bytes, characters outside ASCII (like `é` or `€`) take several bytes. For those, use the `utf8` library (Lesson 32).

---

## 11.9 The Method Call Syntax on Strings (`("x"):upper()`)

All strings share a metatable whose `__index` field is the `string` table. That is why you can call library functions as **methods** on any string value:

```lua
local s = "hello"
print(s:upper())             --> HELLO
print(s:sub(2, 3))           --> el
print(s:rep(2))              --> hellohello
print(("%d items"):format(3))--> 3 items
```

The method form `s:f(args)` means `string.f(s, args)`.

A string **literal** must be wrapped in parentheses before a method call, otherwise the syntax is invalid:

```lua
print(("abc"):upper())       --> ABC
-- print("abc":upper())      -- syntax error
```

Method calls can be chained, which reads naturally from left to right:

```lua
print((" Hello World "):lower():gsub("%s+", "_"))   --> _hello_world_	3
```

Note that `#s` is an operator, not a method, and numbers do not have methods (`(5):type()` fails), but you can add your own string methods:

```lua
function string.shout(s) return s:upper() .. "!" end
print(("hey"):shout())       --> HEY!
```

Extending standard tables is convenient but may conflict with other code, so do it sparingly and document it.

---

## 11.10 Splitting, Trimming, and Joining Strings

Lua has no built-in `split`, `trim`, or `join`, but each is short to write.

**Trim** whitespace from both ends:

```lua
local function trim(s)
  return (s:match("^%s*(.-)%s*$"))
end
print("[" .. trim("   hello  ") .. "]")   --> [hello]
```

**Split** on a separator. This version keeps empty fields and treats the separator as plain text:

```lua
local function split(str, sep)
  local parts, start = {}, 1
  while true do
    local i, j = string.find(str, sep, start, true)
    if not i then
      parts[#parts + 1] = str:sub(start)
      break
    end
    parts[#parts + 1] = str:sub(start, i - 1)
    start = j + 1
  end
  return parts
end

local fields = split("a,b,,d", ",")
print(#fields, fields[1], fields[3] == "", fields[4])   --> 4	a	true	d
```

For simple cases where empty fields should be skipped, `gmatch` is shorter:

```lua
local words = {}
for w in ("the quick  brown fox"):gmatch("%S+") do
  words[#words + 1] = w
end
print(#words)   --> 4
```

**Join** a list of strings with `table.concat`:

```lua
print(table.concat({ "a", "b", "c" }, ", "))   --> a, b, c
print(table.concat(words, "-"))                --> the-quick-brown-fox
```

Other handy helpers:

```lua
local function starts_with(s, prefix) return s:sub(1, #prefix) == prefix end
local function ends_with(s, suffix)   return suffix == "" or s:sub(-#suffix) == suffix end
print(starts_with("lua-5.4", "lua"), ends_with("main.lua", ".lua"))   --> true	true
```

---

[Previous](./[10]-Scope-And-Closures.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[12]-Error-Handling.md)
