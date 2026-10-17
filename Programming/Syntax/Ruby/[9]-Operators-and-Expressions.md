[Previous](./[8]-Symbols.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[10]-Conditionals.md)

*Core Syntax*

# Lesson 9 - Operators & Expressions (arithmetic, comparison, logical, ranges, safe navigation)

An **expression** is code that produces a value. Operators combine values into expressions. In Ruby, almost everything is an expression, and most operators are actually **method calls** in disguise: `5 + 3` is the same as `5.+(3)`.

---

## 9.1 Arithmetic Operators

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `+` | Addition | `7 + 2` | `9` |
| `-` | Subtraction | `7 - 2` | `5` |
| `*` | Multiplication | `7 * 2` | `14` |
| `/` | Division | `7 / 2` | `3` (integers), `3.5` (if a float is involved) |
| `%` | Modulo (remainder) | `7 % 2` | `1` |
| `**` | Exponent | `2 ** 3` | `8` |
| `-x` | Negation | `-5` | `-5` |

```ruby
puts 10 / 3          # 3
puts 10.0 / 3        # 3.3333333333333335
puts 10 % 3          # 1
puts 2 ** 0.5        # 1.4142135623730951
puts -2 ** 2         # -4    (** binds tighter than unary minus)
puts (-2) ** 2       # 4
```

Operators are methods, so you can call them with dot syntax, and other classes define them too:

```ruby
puts 5.+(3)               # 8
puts "ab" + "cd"          # abcd
puts "ab" * 3             # ababab
puts [1, 2] + [3]         # [1, 2, 3]
puts [1, 2, 3] - [2]      # [1, 3]
puts [1, 2] * 2           # [1, 2, 1, 2]
puts "%05.1f" % 3.14159   # 003.1
```

### Bitwise operators (for integers)

| Operator | Meaning | Example |
|----------|---------|---------|
| `&` | AND | `6 & 3` is `2` |
| `\|` | OR | `6 \| 3` is `7` |
| `^` | XOR | `6 ^ 3` is `5` |
| `~` | NOT | `~6` is `-7` |
| `<<` | Shift left | `1 << 3` is `8` |
| `>>` | Shift right | `8 >> 2` is `2` |

`<<` also means "append" for strings and arrays (`[1] << 2`).

---

## 9.2 Comparison Operators and the Spaceship (<=>)

| Operator | Meaning |
|----------|---------|
| `==` | Equal in value |
| `!=` | Not equal |
| `<`, `>` | Less than, greater than |
| `<=`, `>=` | Less or equal, greater or equal |
| `<=>` | Spaceship (combined comparison) |
| `===` | Case equality (used by `case/when`, see Lesson 10 and Lesson 18) |
| `=~`, `!~` | Regex match / no match (Lesson 21) |

```ruby
puts 5 == 5          # true
puts 5 == 5.0        # true   (value equality)
puts 5 != 6          # true
puts 3 < 5           # true
puts "a" < "b"       # true   (alphabetical)
puts "apple" == "apple"   # true
```

Comparison operators return `true` or `false`.

### The spaceship `<=>`

It returns **`-1`**, **`0`**, or **`1`** depending on whether the left side is less than, equal to, or greater than the right side. It returns `nil` if the two cannot be compared.

```ruby
puts 1 <=> 2         # -1
puts 2 <=> 2         # 0
puts 3 <=> 2         # 1
puts "a" <=> "b"     # -1
p 1 <=> "a"          # nil
```

It powers sorting. `sort` uses `<=>`, and you can write your own comparison with it:

```ruby
words = %w[pear fig banana]
p words.sort                               # ["banana", "fig", "pear"]
p words.sort { |a, b| a.length <=> b.length }   # ["fig", "pear", "banana"]
p words.sort { |a, b| b <=> a }            # descending
p words.sort_by { |w| [-w.length, w] }     # longest first, ties alphabetical
```

Include `Comparable` in your own classes and define `<=>` to get `<`, `>`, `between?`, and more (Lesson 30).

---

## 9.3 Logical Operators: &&, ||, and, or, not

| Operator | Meaning |
|----------|---------|
| `&&` | AND: true if **both** are truthy |
| `\|\|` | OR: true if **at least one** is truthy |
| `!` | NOT: flips truthiness |
| `and`, `or`, `not` | Low-precedence versions of the above |

```ruby
puts true && false     # false
puts true || false     # true
puts !true             # false
puts (5 > 3) && (2 > 1)    # true
```

### Short-circuiting and return values

`&&` and `||` stop as soon as the answer is known, and they **return one of their operands**, not just `true` or `false`:

```ruby
puts nil || "default"       # default    (first truthy value)
puts "a" || "b"             # a
puts nil && "x"             # (nil: stops at the first falsy value)
puts "x" && "y"             # y          (last value if all truthy)
puts false || nil           # (nil)
```

This is the basis of the "default value" idiom:

```ruby
name = input || "Guest"
```

Short-circuiting also avoids errors:

```ruby
user = nil
puts user && user.name      # nil, and no NoMethodError
```

### `and` / `or` / `not` versus `&&` / `||` / `!`

They look similar, but **`and` and `or` have very low precedence**, lower than `=`:

```ruby
a = true && false
puts a          # false

b = true and false     # parsed as: (b = true) and false
puts b          # true   (surprise!)
```

Use `&&`, `||`, `!` in conditions. Some Rubyists use `and`/`or` only for **control flow** where the right side is an action:

```ruby
file = File.open("data.txt") or raise "cannot open file"
puts "ok" and return if done
```

Many style guides tell you to avoid `and`/`or` altogether. The safest habit is: always use `&&`, `||`, `!`.

### Truthiness reminder

In Ruby only `false` and `nil` are falsy. Everything else (even `0` and `""`) is truthy (Lesson 18).

---

## 9.4 Assignment and ||= / &&=

`=` assigns a value. Combine it with an operator for a shortcut:

```ruby
x = 10
x += 5     # x = x + 5   (15)
x -= 3     # 12
x *= 2     # 24
x /= 4     # 6
x %= 4     # 2
x **= 3    # 8
x <<= 1    # 16  (shift)
```

### `||=` : assign if currently nil or false

```ruby
name = nil
name ||= "Guest"
puts name       # Guest
name ||= "Other"
puts name       # Guest  (already set, so unchanged)
```

Typical use: set defaults, and **memoize** (cache) a calculation:

```ruby
def settings
  @settings ||= load_settings    # only calls load_settings the first time
end

counts = {}
counts[:ruby] ||= 0
counts[:ruby] += 1
```

Caution: `||=` also replaces a stored `false`. For booleans use `defined?` or `key?`:

```ruby
@flag = false
@flag ||= true     # becomes true!
```

### `&&=` : assign only if currently truthy

```ruby
name = "ada"
name &&= name.capitalize
puts name        # Ada

value = nil
value &&= value.upcase
p value          # nil   (nothing happened)
```

### Multiple assignment and the value of assignment

Assignment is itself an expression that returns the assigned value, so you can chain it or use it in conditions:

```ruby
a = b = 5
if (line = gets)
  puts line
end
```

---

## 9.5 The Safe Navigation Operator (&.)

Calling a method on `nil` raises `NoMethodError`. The **safe navigation operator** `&.` calls the method only if the receiver is not `nil`; otherwise the whole call returns `nil`.

```ruby
user = nil
# user.name        # NoMethodError: undefined method 'name' for nil
puts user&.name.inspect    # nil
```

```ruby
user = { profile: nil }
city = user[:profile]&.fetch(:city, nil)
p city        # nil
```

It can be chained:

```ruby
company&.owner&.address&.city
```

Details:

- It short-circuits the **rest of the chain** on that receiver, so `nil&.foo.bar` does not call `bar` on `nil` in a failing way (the whole expression becomes `nil`).
- It only guards against `nil`. `false` still receives the method call.
- It works with assignment: `user&.name = "Ada"` does nothing if `user` is `nil`.
- Combine it with a default: `user&.name || "Unknown"`.

```ruby
def greeting(user)
  "Hello, #{user&.name || 'stranger'}!"
end
```

More ways to avoid nil errors appear in Lesson 18.

---

## 9.6 Operator Precedence

When an expression has several operators, **precedence** decides which is evaluated first. From **highest** (binds tightest) to **lowest**:

| Level | Operators |
|-------|-----------|
| 1 | `!` `~` unary `+` |
| 2 | `**` |
| 3 | unary `-` |
| 4 | `*` `/` `%` |
| 5 | `+` `-` |
| 6 | `<<` `>>` |
| 7 | `&` |
| 8 | `\|` `^` |
| 9 | `>` `>=` `<` `<=` |
| 10 | `<=>` `==` `===` `!=` `=~` `!~` |
| 11 | `&&` |
| 12 | `\|\|` |
| 13 | `..` `...` |
| 14 | `? :` (ternary) |
| 15 | modifier `rescue` |
| 16 | `=` `+=` `-=` etc. |
| 17 | `defined?` |
| 18 | `not` |
| 19 | `or` `and` |
| 20 | modifier `if` `unless` `while` `until` |

Examples:

```ruby
puts 2 + 3 * 4          # 14  (multiplication first)
puts (2 + 3) * 4        # 20  (parentheses override everything)
puts 2 ** 3 ** 2        # 512 (** is right-associative: 2 ** (3 ** 2))
puts 10 - 4 - 3         # 3   (left-associative: (10 - 4) - 3)
puts -3 ** 2            # -9
puts !true == false     # true  ((!true) == false)
puts 1 + 2 == 3 && 4 > 3   # true
```

Practical advice: when in doubt, **add parentheses**. They cost nothing and make code clearer than relying on the table.

### Ranges as expressions (a first look)

`..` and `...` create ranges, with lower precedence than arithmetic:

```ruby
r1 = 1..5        # 1,2,3,4,5  (includes the end)
r2 = 1...5       # 1,2,3,4    (excludes the end)
r3 = 1..2 + 3    # 1..5       (+ happens first)
```

Ranges are covered fully in Lesson 16.

### Other handy operators

```ruby
x = 5
puts x.positive? ? "pos" : "non-pos"   # ternary (Lesson 10)
puts defined?(x)                       # local-variable
puts defined?(puts)                    # method
puts defined?(String)                  # expression / constant
puts Math::PI                          # :: looks up constants and namespaces
puts [1, 2, 3][1]                      # [] is a method call too
puts "abc" =~ /b/                      # 1 (index of the match)
```

---

[Previous](./[8]-Symbols.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[10]-Conditionals.md)