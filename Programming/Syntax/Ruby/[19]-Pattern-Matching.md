[Previous](./[18]-Truthiness-nil-and-Equality.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[20]-Error-Handling.md)

*Core Syntax*

# Lesson 19 - Pattern Matching (case/in, destructuring)

**Pattern matching** checks whether a value has a certain *shape* and, at the same time, **pulls pieces out** of it into variables. It is especially useful for arrays, hashes, JSON-like data, and objects. It uses `case ... in` (not `case ... when`).

---

## 19.1 case / in Basics

```ruby
value = 5

case value
in 0
  puts "zero"
in 1..9
  puts "single digit"
in Integer
  puts "big integer"
else
  puts "something else"
end
# single digit
```

Differences from `case/when`:

| `case/when` | `case/in` |
|-------------|-----------|
| Compares with `===` | Matches **patterns** (and uses `===` for value patterns) |
| Cannot extract parts | Can bind variables as it matches |
| No error if nothing matches (returns `nil`) | Raises **`NoMatchingPatternError`** if nothing matches and there is no `else` |

```ruby
case 42
in String then "text"
end
# NoMatchingPatternError (42)
```

### Kinds of patterns

| Pattern | Matches |
|---------|---------|
| `5`, `"hi"`, `:sym`, `nil` | A value equal to it (`===`) |
| `1..5`, `..10` | A value inside the range |
| `Integer`, `String` | An instance of the class |
| `/regex/` | A string matching the regex |
| `->(x) { x > 3 }` | A value for which the lambda returns true |
| `a` (a bare name) | **Anything**, and binds it to a variable `a` |
| `_` | Anything, ignoring it |
| `[ ... ]` | An array shape |
| `{ ... }` | A hash shape |
| `pat1 \| pat2` | Either pattern |
| `pattern => name` | The pattern, and also binds the value to `name` |
| `^name`, `^(expr)` | The value of an existing variable or expression (pin) |

Binding the value:

```ruby
case 7
in Integer => n
  puts "got #{n}"
end

case "hello"
in String => s if s.length > 3
  puts "long string: #{s}"
end
```

`case/in` is an **expression**, so you can assign its result:

```ruby
kind = case value
       in Integer | Float then :number
       in String          then :text
       in nil             then :nothing
       else                    :other
       end
```

---

## 19.2 Array and Hash Patterns

### Array patterns

An array pattern matches arrays of an **exact length** (unless it uses `*`), checking each position:

```ruby
case [1, 2, 3]
in [a, b, c]
  puts "a=#{a} b=#{b} c=#{c}"
end

case [1, [2, 3]]
in [x, [y, z]]               # nested
  puts x + y + z             # 6
end

case [:ok, 200]
in [:ok, Integer => code]
  puts "success #{code}"
in [:error, message]
  puts "failed: #{message}"
end
```

Splats capture the rest:

```ruby
case [1, 2, 3, 4, 5]
in [first, *rest]
  p first      # 1
  p rest       # [2, 3, 4, 5]
end

case [1, 2, 3, 4, 5]
in [first, *, last]
  puts "#{first}..#{last}"     # 1..5
end

case []
in []
  puts "empty array"
end
```

### Hash patterns

A hash pattern matches hashes that **contain at least those keys** (extra keys are fine). Keys must be **symbols**:

```ruby
user = { name: "Ada", age: 36, role: :admin }

case user
in { name: String => name, age: Integer => age }
  puts "#{name} is #{age}"
end

case user
in { role: :admin }
  puts "admin access"
in { role: :guest }
  puts "guest access"
end
```

Binding by key name (shorthand):

```ruby
case user
in { name:, age: }          # creates local variables name and age
  puts "#{name}, #{age}"
end
```

Nested data (typical JSON shape):

```ruby
response = {
  status: "ok",
  data: { user: { id: 7, emails: ["a@x.com", "b@x.com"] } }
}

case response
in { status: "ok", data: { user: { id:, emails: [first_email, *] } } }
  puts "user #{id}, first email #{first_email}"
in { status: "error", message: }
  puts "error: #{message}"
end
```

Capturing the remaining keys, or forbidding them:

```ruby
case { a: 1, b: 2, c: 3 }
in { a: 1, **others }
  p others             # {b: 2, c: 3}
end

case { a: 1 }
in { a: Integer, **nil }     # matches only if there are NO other keys
  puts "exactly a"
end

case {}
in {}                         # {} matches ONLY an empty hash
  puts "empty hash"
end
```

---

## 19.3 Guards, Alternatives, and the Pin Operator

### Guards: `if` and `unless`

Add an extra condition after the pattern:

```ruby
case [3, 4]
in [x, y] if x + y > 5
  puts "big sum"
in [x, y] unless x == y
  puts "different"
end
```

### Alternatives with `|`

```ruby
case status
in :ok | :success | :done
  puts "good"
in :error | :failed
  puts "bad"
end

case value
in Integer | Float => number
  puts "numeric: #{number}"
end

case [1, "x"]
in [Integer | Float, String | Symbol]
  puts "matched alternatives"
end
```

Alternatives cannot bind variables (except `_`-style ones), because Ruby could not know which one matched.

### The pin operator `^`

A bare name in a pattern **creates a new variable** (it matches anything). To match against the value of an *existing* variable, **pin** it with `^`:

```ruby
expected = 5

case 5
in ^expected
  puts "matches the existing value 5"
end

case 7
in expected        # no pin: this REBINDS expected to 7 and always matches!
  puts expected
end
```

Pin expressions and instance variables:

```ruby
limit = 10
case 11
in Integer => n if n > limit
  puts "over"
end

case [1, 2]
in [a, ^(a + 1)]           # pin an expression: second item must be first + 1
  puts "consecutive"
end

@target = 3
case 3
in ^@target
  puts "matches instance variable"
end
```

Pinning is how you compare against dynamic values inside a larger pattern:

```ruby
current_id = 42
case { id: 42, name: "Ada" }
in { id: ^current_id, name: }
  puts "found #{name}"
end
```

---

## 19.4 Find Patterns

A **find pattern** looks for a sequence **anywhere** inside an array. It is written with a splat on each side:

```ruby
case [1, 42, "x", :y, 99]
in [*, String => text, Symbol => sym, *]
  puts "found #{text} followed by #{sym}"
end
# found x followed by y

case ["a", 1, "b", 2]
in [*before, Integer => n, *after]
  p before    # ["a"]
  p n         # 1      (the FIRST integer)
  p after     # ["b", 2]
end
```

It is handy for scanning lists of records:

```ruby
events = [
  { type: "click", x: 1 },
  { type: "error", message: "boom" },
  { type: "click", x: 2 }
]

case events
in [*, { type: "error", message: }, *]
  puts "first error: #{message}"
end
```

Remember:

- The pattern must have exactly **two splats**, one at each end, with at least one pattern in the middle.
- It finds the **first** place that matches.
- Find patterns were introduced as an experimental feature in older versions, so very old Ruby versions may print a warning.

---

## 19.5 Custom Matching with deconstruct and deconstruct_keys

Array and hash patterns also work on **your own objects**, if they say how to be taken apart.

- `deconstruct` returns an **array** (used by array patterns `[...]`).
- `deconstruct_keys(keys)` returns a **hash** (used by hash patterns `{...}`). The argument is the array of keys the pattern asks for (or `nil` when it uses `**rest`).

```ruby
class Point
  attr_reader :x, :y

  def initialize(x, y)
    @x = x
    @y = y
  end

  def deconstruct
    [x, y]
  end

  def deconstruct_keys(keys)
    { x: x, y: y }
  end
end

point = Point.new(3, 4)

case point
in [x, y]
  puts "array style: #{x}, #{y}"
end

case point
in { x:, y: }
  puts "hash style: #{x}, #{y}"
end
```

### Class-prefixed patterns

Write a class name before the brackets to check the type **and** the shape:

```ruby
case point
in Point(x:, y:)           # same as Point === point and { x:, y: }
  puts "Point at #{x},#{y}"
end

case point
in Point[a, b]             # square brackets use deconstruct
  puts "#{a},#{b}"
end

case point
in Point(x: 0, y: 0)
  puts "origin"
in Point(x:, y: 0)
  puts "on the x axis at #{x}"
end
```

### Struct and Data support it automatically

```ruby
Coord = Struct.new(:lat, :lng)
case Coord.new(1.5, 2.5)
in [lat, lng]
  puts "lat #{lat}"
end

Person = Data.define(:name, :age)
case Person.new(name: "Ada", age: 36)
in { name:, age: }
  puts "#{name} #{age}"
end

case Person.new(name: "Ada", age: 36)
in Person[name: String => n]
  puts n
end
```

Many standard classes (such as `Time`, `Date`, and `MatchData`) provide `deconstruct_keys` too:

```ruby
case Time.new(2026, 10, 17)
in { year:, month: 10, day: }
  puts "October #{day}, #{year}"
end
```

---

## 19.6 Standalone Matching with => and in

You don't need a full `case` for a single pattern.

### `expression => pattern` : match or raise

It **destructures** and binds variables, and raises `NoMatchingPatternError` (or `NoMatchingPatternKeyError`) if it doesn't fit:

```ruby
config = { db: { host: "localhost", port: 5432 } }

config => { db: { host:, port: } }
puts "#{host}:#{port}"          # localhost:5432

[1, [2, 3]] => [a, [b, c]]
puts a + b + c                  # 6

# { name: "Ada" } => { age: }   # NoMatchingPatternKeyError: key not found: :age
```

Great for **asserting a shape** (like validating an API response) while unpacking it.

### `expression in pattern` : returns true or false

```ruby
result = { status: "ok", code: 200 }

puts(result in { status: "ok" })      # true
puts(result in { status: "error" })   # false
puts(5 in Integer)                    # true
puts("x" in Integer)                  # false

if result in { code: 200..299 => code }
  puts "success #{code}"             # variables bind when it matches
end
```

Use `in` as a safe boolean test, in `if`, `select`, and so on:

```ruby
users = [{ role: :admin }, { role: :guest }, { role: :admin, active: true }]
admins = users.select { |u| u in { role: :admin } }
puts admins.size    # 2
```

### When to use which

| Situation | Use |
|-----------|-----|
| Several shapes to choose between | `case ... in` |
| Unpack data you *expect* to have a shape | `value => pattern` |
| Yes/no test of a shape | `value in pattern` |

### Common mistakes

- Using `when` and `in` in the same `case`: not allowed.
- Forgetting that bare names **bind** (rather than compare). Use `^pin`.
- Using string keys in hash patterns: only **symbol** keys are supported (convert first with `transform_keys(&:to_sym)` or parse JSON with `symbolize_names: true`).
- Expecting a hash pattern to need *all* keys of the hash. It only needs the listed ones. Use `**nil` for "no others".
- Expecting an array pattern to match a longer array. Array patterns need an exact length unless they use `*`.

---

[Previous](./[18]-Truthiness-nil-and-Equality.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[20]-Error-Handling.md)