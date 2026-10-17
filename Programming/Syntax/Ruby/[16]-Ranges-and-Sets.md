[Previous](./[15]-Hashes.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[17]-Enumerable-and-Enumerators.md)

*Core Syntax*

# Lesson 16 - Ranges & Sets

A **Range** represents an interval of values, such as `1..10` or `"a".."z"`. A **Set** is a collection of **unique** values with fast membership tests and mathematical set operations.

---

## 16.1 Inclusive and Exclusive Ranges

Two dots `..` **include** the end value. Three dots `...` **exclude** it.

```ruby
inclusive = 1..5      # 1, 2, 3, 4, 5
exclusive = 1...5     # 1, 2, 3, 4

inclusive.to_a        # [1, 2, 3, 4, 5]
exclusive.to_a        # [1, 2, 3, 4]
```

Ranges can be made from anything that has an order and a "next" value (it implements `<=>` and `succ`):

```ruby
("a".."e").to_a       # ["a", "b", "c", "d", "e"]
("aa".."ad").to_a     # ["aa", "ab", "ac", "ad"]
(1.0..2.0)            # a float range (cannot be iterated, only tested)
(Time.now..Time.now + 3600)   # a range of times
```

### Common methods

```ruby
r = (1..10)

r.first               # 1
r.last                # 10
r.first(3)            # [1, 2, 3]
r.last(3)             # [8, 9, 10]
r.min                 # 1
r.max                 # 10
r.size                # 10   (also count)
r.sum                 # 55
r.include?(5)         # true  (also member?, ===)
r.cover?(5)           # true
r.cover?(2..4)        # true  (does it contain a whole range?)
r.begin               # 1
r.end                 # 10
r.exclude_end?        # false
(1...10).max          # 9
```

### Iteration and stepping

```ruby
(1..3).each { |n| puts n }
(1..10).step(3).to_a        # [1, 4, 7, 10]
(1..10) % 3                 # same as step: ((1..10).%(3))
(0.0..1.0).step(0.25).to_a  # [0.0, 0.25, 0.5, 0.75, 1.0]
('a'..'e').step(2).to_a     # ["a", "c", "e"]
(1..5).reverse_each { |n| print n }   # 54321
(1..5).map { |n| n * n }              # all Enumerable methods work
```

### `include?` versus `cover?`

- `cover?` only checks whether a value lies between the begin and end values (fast).
- `include?` on numeric ranges behaves like `cover?`. On string ranges, it may check each element.

```ruby
("a".."z").cover?("bb")      # true   ("bb" sorts between "a" and "z")
("a".."z").include?("bb")    # false  ("bb" is not one of the elements)
```

### Creating ranges explicitly

```ruby
Range.new(1, 5)         # 1..5
Range.new(1, 5, true)   # 1...5
(1..5).to_a
[*1..5]
```

Parentheses matter with method calls: `(1..5).each`, not `1..5.each`.

---

## 16.2 Endless and Beginless Ranges

Leave out one end to represent "no limit".

```ruby
endless = (1..)       # 1, 2, 3, ... forever
beginless = (..10)    # up to 10
also_endless = (1..nil)
```

### Uses

Slicing arrays and strings:

```ruby
a = %w[a b c d e]
a[2..]        # ["c", "d", "e"]
a[..2]        # ["a", "b", "c"]
a[..-2]       # everything except the last
"hello"[1..]  # "ello"
```

Conditions and `case`:

```ruby
case age
when ..12  then "child"
when 13..19 then "teen"
when 20..   then "adult"
end

(18..) === 21     # true
(..0).cover?(-5)  # true
```

Infinite iteration, which must be stopped:

```ruby
(1..).each do |n|
  puts n
  break if n >= 3
end

(1..).first(3)         # [1, 2, 3]
(1..).lazy.map { |n| n * n }.select(&:even?).first(3)   # [4, 16, 36]
(1..).step(5).first(3) # [1, 6, 11]
```

Careful: `(1..).to_a`, `(1..).size` (is `Infinity`), `.sum`, and `.map` without `lazy` never finish or fail. A beginless range cannot be iterated at all, since there is no starting point.

### Float::INFINITY

An older way of writing an endless range:

```ruby
(1..Float::INFINITY).lazy.map { |n| n * 2 }.first(3)
```

---

## 16.3 Ranges in Conditions and case

Range membership is a clean way to test numeric ranges.

```ruby
score = 85

if (80..89).include?(score)
  puts "B"
end

puts "passed" if score.between?(60, 100)

puts "valid" if (1..12) === month      # === asks "is month inside the range?"
```

`case/when` uses `===`, so ranges work naturally:

```ruby
grade = case score
        when 90..100 then "A"
        when 80...90 then "B"
        when 70...80 then "C"
        when 0...70  then "F"
        else "invalid"
        end
```

Clamping values to a range:

```ruby
15.clamp(1..10)       # 10
-5.clamp(0..)         # 0
3.clamp(..2)          # 2
```

Random numbers from a range:

```ruby
rand(1..6)            # like rolling a die
rand(0.0..1.0)
```

Ranges also work for other objects, such as dates:

```ruby
require "date"
holiday = Date.new(2026, 12, 20)..Date.new(2027, 1, 5)
holiday.cover?(Date.new(2026, 12, 25))   # true
holiday.count                            # 17 days
```

Ranges in regular-expression-free text checks are common too:

```ruby
("a".."z").cover?("m")    # true
```

---

## 16.4 Working with Set

A **Set** is an unordered-in-concept (but insertion-ordered in practice) collection of **unique** elements, backed by a hash. Membership tests are very fast.

```ruby
require "set"      # automatic in newer Ruby versions, but harmless in all of them

colors = Set.new(["red", "green", "red"])
p colors            # #<Set: {"red", "green"}>
colors.size         # 2

Set[1, 2, 3]            # shortcut constructor
[1, 2, 2, 3].to_set     # converts an array
(1..5).to_set
```

### Adding, removing, checking

```ruby
s = Set.new
s << 1               # add
s.add(2)             # add (returns the set)
s.add?(2)            # nil if it was already present, otherwise the set
s.add?(3)
s.delete(1)
s.delete?(99)        # nil if absent
s.include?(2)        # true   (also member?, ===, and contain?)
s.size               # 2      (also length, count)
s.empty?             # false
s.to_a               # [2, 3]
s.merge([4, 5])      # add many elements
s.clear
```

### Iterating and transforming

```ruby
Set[1, 2, 3].each { |n| puts n }
Set[1, 2, 3].map { |n| n * 2 }              # array: [2, 4, 6]
Set[1, 2, 3].select(&:odd?)                 # Set {1, 3}
Set[1, 2, 3].sum
Set[3, 1, 2].sort                           # sets can be sorted into an array
Set[1, 2, 3].group_by(&:odd?)
```

All of the `Enumerable` methods are available (Lesson 17).

### Why use a Set?

1. **Remove duplicates** while keeping a collection:

```ruby
visited = Set.new
visited << "page1"
visited << "page1"      # ignored
puts visited.size       # 1
```

2. **Fast lookups**: `set.include?(x)` is roughly constant time, while `array.include?(x)` scans the whole array.

```ruby
allowed = Set.new(%w[admin editor viewer])
puts "ok" if allowed.include?(role)
```

3. **Set math** (next section).

### Elements and mutability

- Elements are compared like hash keys (`eql?` and `hash`). `1` and `1.0` are different elements.
- Don't change an element after adding it, or the set can lose track of it.
- A set can be frozen: `Set[1, 2].freeze`.
- A set is not indexed: `set[0]` does not work. Convert with `to_a` if you need positions.

---

## 16.5 Set Operations: Union, Intersection, Difference

Given:

```ruby
a = Set[1, 2, 3, 4]
b = Set[3, 4, 5, 6]
```

| Operation | Operators / methods | Result |
|-----------|--------------------|--------|
| **Union** (everything in either) | `a \| b`, `a.union(b)`, `a + b` | `{1, 2, 3, 4, 5, 6}` |
| **Intersection** (in both) | `a & b`, `a.intersection(b)` | `{3, 4}` |
| **Difference** (in a, not in b) | `a - b`, `a.difference(b)` | `{1, 2}` |
| **Symmetric difference** (in one, not both) | `a ^ b` | `{1, 2, 5, 6}` |

```ruby
p a | b      # #<Set: {1, 2, 3, 4, 5, 6}>
p a & b      # #<Set: {3, 4}>
p a - b      # #<Set: {1, 2}>
p a ^ b      # #<Set: {5, 6, 1, 2}>   (element order may vary)
```

They work with any enumerable on the right side:

```ruby
a | [10, 11]       # {1, 2, 3, 4, 10, 11}
```

### Comparing sets

```ruby
small = Set[1, 2]
big = Set[1, 2, 3]

small.subset?(big)           # true   (also <=)
small.proper_subset?(big)    # true   (also <)
big.superset?(small)         # true   (also >=)
big.proper_superset?(small)  # true   (also >)
small.disjoint?(Set[8, 9])   # true   (nothing in common)
small.intersect?(big)        # true   (something in common)
small == Set[2, 1]           # true   (order does not matter)
small <=> big                # -1     (subset)
```

### In-place versions

```ruby
a.merge(b)          # union into a
a.subtract(b)       # remove b's elements from a
a.delete_if { |n| n.odd? }
a.select! { |n| n > 2 }
a.reject! { |n| n > 4 }
```

### Example: comparing two lists

```ruby
mine = Set.new(%w[ruby python go])
yours = Set.new(%w[python rust go])

puts "Both know: #{(mine & yours).to_a.join(', ')}"      # python, go
puts "Only I know: #{(mine - yours).to_a.join(', ')}"     # ruby
puts "Everyone knows: #{(mine | yours).size} languages"   # 4
```

---

[Previous](./[15]-Hashes.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[17]-Enumerable-and-Enumerators.md)