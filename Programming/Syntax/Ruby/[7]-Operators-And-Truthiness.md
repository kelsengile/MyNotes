[Previous](./[6]-Symbols-Ranges-And-Nil.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[8]-Conditionals-And-Pattern-Matching.md)

*Control Flow*

# Lesson 7 - Operators And Truthiness

## 7.1 Operators Are Methods

Most Ruby operators are method calls with special syntax:

```ruby
1 + 2        # same as 1.+(2)
"a" * 3      # same as "a".*(3)
[1] << 2     # same as [1].<<(2)
```

That means classes can define their own operators (see Lesson 15):

```ruby
class Point
  attr_reader :x, :y
  def initialize(x, y)
    @x = x
    @y = y
  end

  def +(other)
    Point.new(x + other.x, y + other.y)
  end
end
```

---

## 7.2 Comparison Operators

| Operator | Meaning |
| --- | --- |
| `==` | Equal in value |
| `!=` | Not equal |
| `<`, `>`, `<=`, `>=` | Ordering |
| `<=>` | Spaceship: returns `-1`, `0`, `1`, or `nil` |
| `===` | Case equality (used by `case/when`) |
| `=~`, `!~` | Regex match / non-match |
| `eql?` | Same value and same type (used by `Hash`) |
| `equal?` | Same object identity |

```ruby
1 == 1.0        #=> true
1.eql?(1.0)     #=> false
1.equal?(1)     #=> true  (small integers are the same object)
"a".equal?("a") #=> false
5 <=> 10        #=> -1
"b" <=> "a"     #=> 1
1 <=> "a"       #=> nil
Integer === 3   #=> true
(1..5) === 3    #=> true
/ab/ === "cab"  #=> true
```

---

## 7.3 Logical Operators

| Operator | Meaning | Precedence |
| --- | --- | --- |
| `&&` | AND | high |
| `\|\|` | OR | high |
| `!` | NOT | very high |
| `and` | AND | very low |
| `or` | OR | very low |
| `not` | NOT | low |

`&&` and `||` **short-circuit** and return the deciding operand:

```ruby
nil || "default"    #=> "default"
"a" && "b"          #=> "b"
false && puts("never printed")
```

Use `&&`/`||` for boolean expressions. `and`/`or` are for control flow, where their low precedence is useful:

```ruby
user = find_user or raise "not found"
```

Mixing them causes surprises:

```ruby
x = true && false     # x is false
y = true and false    # y is true!  (parsed as (y = true) and false)
```

---

## 7.4 Assignment Operators

```ruby
x = 5
x += 2    # 7
x -= 1    # 6
x *= 2    # 12
x /= 4    # 3
x %= 2    # 1
x **= 3   # 1
x <<= 2   # 4 (bit shift)

a ||= 10  # assign if a is nil/false
a &&= 20  # assign if a is truthy

h = {}
h[:count] ||= 0
h[:count] += 1
```

---

## 7.5 Bitwise And Other Operators

```ruby
6 & 3     #=> 2     (AND)
6 | 3     #=> 7     (OR)
6 ^ 3     #=> 5     (XOR)
~6        #=> -7    (NOT)
1 << 4    #=> 16    (left shift)
16 >> 2   #=> 4     (right shift)
5[0]      #=> 1     (bit reference)
```

Other operators:

```ruby
cond ? "yes" : "no"       # ternary
obj&.method               # safe navigation
Foo::BAR                  # scope resolution
..  ...                   # range construction
defined?(x)               # definition check
[1, 2] | [2, 3]           #=> [1, 2, 3] (set union)
[1, 2] & [2, 3]           #=> [2]       (set intersection)
[1, 2, 3] - [2]           #=> [1, 3]
```

---

## 7.6 Operator Precedence

From highest to lowest (operators on the same row have equal precedence):

| Level | Operators |
| --- | --- |
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

```ruby
2 + 3 * 4       #=> 14
(2 + 3) * 4     #=> 20
-2 ** 2         #=> -4   (** binds tighter than unary minus)
2 ** 3 ** 2     #=> 512  (right associative)
```

When in doubt, use parentheses.

---

[Previous](./[6]-Symbols-Ranges-And-Nil.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[8]-Conditionals-And-Pattern-Matching.md)
