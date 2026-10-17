[Previous](./[13]-Blocks-Procs-and-Lambdas.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[15]-Hashes.md)

*Core Syntax*

# Lesson 14 - Arrays

An **Array** is an ordered, indexed list of objects. Arrays can hold anything (even mixed types) and grow or shrink as needed. They are one of the most used classes in Ruby.

---

## 14.1 Creating Arrays

```ruby
empty = []
numbers = [1, 2, 3]
mixed = [1, "two", :three, 4.0, nil, [5, 6]]
```

### Array.new

```ruby
Array.new            # []
Array.new(3)         # [nil, nil, nil]
Array.new(3, 0)      # [0, 0, 0]
Array.new(3) { |i| i * i }   # [0, 1, 4]   (block runs for each index)
```

**Pitfall:** the two-argument form puts the **same object** in every slot:

```ruby
bad = Array.new(3, "x")
bad[0] << "!"
p bad                 # ["x!", "x!", "x!"]  all three are one string!

good = Array.new(3) { "x" }
good[0] << "!"
p good                # ["x!", "x", "x"]
```

### Word and symbol arrays

```ruby
%w[apple banana cherry]       # ["apple", "banana", "cherry"]
%i[red green blue]            # [:red, :green, :blue]
```

### From other objects

```ruby
(1..5).to_a           # [1, 2, 3, 4, 5]
"a,b,c".split(",")    # ["a", "b", "c"]
"abc".chars           # ["a", "b", "c"]
{ a: 1 }.to_a         # [[:a, 1]]
Array(nil)            # []
Array([1, 2])         # [1, 2]
Array(1..3)           # [1, 2, 3]
Array("a")            # ["a"]       (wraps single values)
[*1..3, *"a".."c"]    # [1, 2, 3, "a", "b", "c"]  (splat)
```

### Basic information

```ruby
a = [10, 20, 30]
a.length       # 3  (also size)
a.count        # 3
a.empty?       # false
a.include?(20) # true
a.class        # Array
```

---

## 14.2 Accessing and Slicing Elements

Indexes start at **0**. Negative indexes count from the end.

```ruby
fruits = %w[apple banana cherry date]

fruits[0]         # "apple"
fruits[2]         # "cherry"
fruits[-1]        # "date"
fruits[-2]        # "cherry"
fruits[10]        # nil   (out of range: no error)
fruits.first      # "apple"
fruits.last       # "date"
fruits.at(1)      # "banana"
```

Safe access with a default or an error:

```ruby
fruits.fetch(1)               # "banana"
fruits.fetch(10, "none")      # "none"
fruits.fetch(10) { |i| "no item #{i}" }
# fruits.fetch(10)            # IndexError
```

### Slicing

```ruby
fruits[1, 2]       # ["banana", "cherry"]   start index, length
fruits[1..2]       # ["banana", "cherry"]   range
fruits[1...3]      # ["banana", "cherry"]   exclusive end
fruits[1..]        # ["banana", "cherry", "date"]  to the end
fruits[..1]        # ["apple", "banana"]
fruits[-2..]       # ["cherry", "date"]
fruits.first(2)    # ["apple", "banana"]
fruits.last(2)     # ["cherry", "date"]
fruits.take(3)     # first 3
fruits.drop(3)     # everything but the first 3
fruits.values_at(0, 2, -1)   # ["apple", "cherry", "date"]
fruits.slice(1, 2)           # same as [1, 2]
```

### Nested access

```ruby
data = [[1, 2], [3, [4, 5]]]
data[1][1][0]          # 4
data.dig(1, 1, 0)      # 4  (returns nil instead of raising if a level is missing)
```

### Random picks

```ruby
fruits.sample          # a random element
fruits.sample(2)       # two random elements
fruits.shuffle         # a shuffled copy
```

---

## 14.3 Adding, Removing, and Modifying

### Adding

```ruby
a = [1, 2, 3]
a << 4                 # [1, 2, 3, 4]   append (most common)
a.push(5, 6)           # [1, 2, 3, 4, 5, 6]
a.append(7)            # same as push
a.unshift(0)           # [0, 1, ...]    add to the front
a.prepend(-1)          # same as unshift
a.insert(2, :x)        # insert at index 2
a.concat([8, 9])       # add all elements of another array
```

### Removing

```ruby
a = [1, 2, 3, 4, 5]
a.pop              # 5   removes and returns the last
a.shift            # 1   removes and returns the first
a.pop(2)           # [3, 4]
a.delete(2)        # removes every 2, returns 2 (or nil)
a.delete_at(0)     # removes the item at an index
a.slice!(1, 2)     # removes and returns a slice
a.clear            # []

b = [1, nil, 2, nil, 3]
b.compact          # [1, 2, 3]     new array without nils
c = [1, 2, 2, 3, 3]
c.uniq             # [1, 2, 3]
d = [[1, [2]], [3]]
d.flatten          # [1, 2, 3]
d.flatten(1)       # [1, [2], 3]   one level only
[1, 2, 3, 4].delete_if(&:even?)   # [1, 3]  (modifies)
[1, 2, 3, 4].reject!(&:even?)     # [1, 3]
[1, 2, 3, 4].keep_if(&:even?)     # [2, 4]
```

### Modifying elements

```ruby
a = [10, 20, 30]
a[1] = 99              # [10, 99, 30]
a[5] = 1               # [10, 99, 30, nil, nil, 1]  (gap filled with nil)
a[0..1] = [:a, :b, :c] # replace a slice with more elements
a.fill(0)              # every element becomes 0
a.replace([1, 2])      # swap the whole contents
a.rotate               # [2, 1]
a.reverse              # [1, 2] (new array); reverse! changes in place
```

### Combining arrays (operators)

```ruby
[1, 2] + [3]            # [1, 2, 3]
[1, 2, 3] - [2]         # [1, 3]
[1, 2] * 2              # [1, 2, 1, 2]
[1, 2] * ","            # "1,2"  (same as join)
[1, 2, 3] & [2, 3, 4]   # [2, 3]       intersection
[1, 2] | [2, 3]         # [1, 2, 3]    union
[1, 2] <=> [1, 3]       # -1
```

> Methods ending in `!` (like `sort!`, `map!`, `compact!`, `uniq!`) change the array itself, and often return `nil` if nothing changed.

---

## 14.4 Iterating and Transforming

```ruby
nums = [1, 2, 3, 4, 5]

nums.each { |n| puts n }
nums.each_with_index { |n, i| puts "#{i}: #{n}" }
nums.each.with_index(1) { |n, i| puts "#{i}. #{n}" }   # start counting at 1
nums.reverse_each { |n| puts n }
nums.each_slice(2) { |pair| p pair }       # [1, 2] [3, 4] [5]
nums.each_cons(2) { |pair| p pair }        # [1, 2] [2, 3] [3, 4] [4, 5]
```

### Transforming with map

`map` (also `collect`) returns a **new array** from the block's results:

```ruby
nums.map { |n| n * 2 }            # [2, 4, 6, 8, 10]
nums.map(&:to_s)                  # ["1", "2", "3", "4", "5"]
nums.map.with_index { |n, i| n * i }
nums.map! { |n| n + 1 }           # changes nums itself
```

`flat_map` maps then flattens one level; `zip` pairs arrays; `product` makes combinations:

```ruby
[[1, 2], [3]].flat_map { |a| a.map { |n| n * 10 } }   # [10, 20, 30]
[1, 2].zip([:a, :b])              # [[1, :a], [2, :b]]
[1, 2].zip([:a, :b], [:x, :y])    # [[1, :a, :x], [2, :b, :y]]
[1, 2].product([:a, :b])          # [[1, :a], [1, :b], [2, :a], [2, :b]]
[1, 2, 3].combination(2).to_a     # [[1, 2], [1, 3], [2, 3]]
[1, 2, 3].permutation(2).count    # 6
[[1, 2], [3, 4]].transpose        # [[1, 3], [2, 4]]
```

### Combining into one value

```ruby
nums.sum                      # 15
nums.reduce(:+)               # 15
nums.reduce(1) { |product, n| product * n }   # 120
nums.join(", ")               # "1, 2, 3, 4, 5"
nums.inject { |a, b| a > b ? a : b }
```

### Converting

```ruby
[[:a, 1], [:b, 2]].to_h       # {a: 1, b: 2}
%w[a b].each_with_index.to_h  # {"a"=>0, "b"=>1}
nums.to_a                     # itself
nums.inspect                  # "[1, 2, 3, 4, 5]"
```

The full set of iteration tools (shared by hashes and ranges) is in Lesson 17.

---

## 14.5 Sorting, Searching, and Filtering

### Sorting

```ruby
[3, 1, 2].sort                      # [1, 2, 3]
[3, 1, 2].sort.reverse              # [3, 2, 1]
[3, 1, 2].sort { |a, b| b <=> a }   # [3, 2, 1]
%w[pear fig banana].sort_by(&:length)         # ["fig", "pear", "banana"]
%w[pear fig banana].sort_by { |w| -w.length } # ["banana", "pear", "fig"]
people.sort_by { |p| [p[:age], p[:name]] }    # sort by several keys
[3, 1, 2].sort!                     # sorts in place
[3, 1, 2].min                       # 1
[3, 1, 2].max                       # 3
[3, 1, 2].minmax                    # [1, 3]
%w[pear fig].min_by(&:length)       # "fig"
[3, 1, 2].max(2)                    # [3, 2]
```

### Searching

```ruby
a = [5, 8, 12, 8]
a.include?(8)         # true
a.index(8)            # 1       first position
a.rindex(8)           # 3       last position
a.find_index { |n| n > 6 }   # 1
a.find { |n| n > 6 }          # 8   (also detect)
a.count(8)            # 2
a.count(&:even?)      # 3
[1, 3, 5, 7].bsearch { |n| n >= 4 }   # 5  (binary search on sorted data)
```

### Filtering

```ruby
nums = [1, 2, 3, 4, 5, 6]
nums.select(&:even?)         # [2, 4, 6]      (also filter)
nums.reject(&:even?)         # [1, 3, 5]
nums.partition(&:even?)      # [[2, 4, 6], [1, 3, 5]]
nums.take_while { |n| n < 4 }  # [1, 2, 3]
nums.drop_while { |n| n < 4 }  # [4, 5, 6]
nums.find_all { |n| n > 4 }    # [5, 6]
nums.filter_map { |n| n * 2 if n.odd? }   # [2, 6, 10]
nums.group_by(&:odd?)        # {true=>[1, 3, 5], false=>[2, 4, 6]}
nums.tally                   # counts of each value
%w[a b a].tally              # {"a"=>2, "b"=>1}
```

### Testing the whole array

```ruby
nums.any? { |n| n > 5 }     # true
nums.all? { |n| n > 0 }     # true
nums.none?(&:zero?)         # true
nums.one? { |n| n == 3 }    # true
nums.any?                   # true (any truthy element)
```

---

## 14.6 Multidimensional Arrays

Ruby has no special 2-D type. You use **arrays of arrays**:

```ruby
grid = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
]

grid[1][2]            # 6   (row 1, column 2)
grid[0]               # [1, 2, 3]
grid.map { |row| row[0] }    # first column: [1, 4, 7]
grid.transpose        # swap rows and columns
grid.flatten          # [1, 2, 3, 4, 5, 6, 7, 8, 9]
grid.sum { |row| row.sum }   # 45
```

Iterating:

```ruby
grid.each do |row|
  row.each { |cell| print cell, " " }
  puts
end

grid.each_with_index do |row, r|
  row.each_with_index do |cell, c|
    puts "(#{r},#{c}) = #{cell}"
  end
end
```

### Creating a blank grid: the shared-reference trap

```ruby
bad = Array.new(3, Array.new(3, 0))
bad[0][0] = 1
p bad     # [[1, 0, 0], [1, 0, 0], [1, 0, 0]]  all rows are the SAME array!

good = Array.new(3) { Array.new(3, 0) }
good[0][0] = 1
p good    # [[1, 0, 0], [0, 0, 0], [0, 0, 0]]
```

### Copying nested arrays

`dup` makes a **shallow** copy: inner arrays are still shared (Lesson 32). For a deep copy of simple data:

```ruby
copy = Marshal.load(Marshal.dump(grid))
copy2 = grid.map(&:dup)      # fine for exactly two levels
```

### Rows with different lengths ("jagged" arrays)

```ruby
jagged = [[1], [2, 3], [4, 5, 6]]
jagged.map(&:length)         # [1, 2, 3]
```

---

## 14.7 Destructuring and Splatting Arrays

### Destructuring assignment

```ruby
a, b = [1, 2]                 # a=1, b=2
first, *rest = [1, 2, 3]      # first=1, rest=[2, 3]
*init, last = [1, 2, 3]       # init=[1, 2], last=3
head, *, tail = [1, 2, 3, 4]  # head=1, tail=4
first, = [10, 20]             # first=10 (trailing comma ignores the rest)
x, (y, z), w = 1, [2, 3], 4   # nested
```

### Destructuring in blocks

```ruby
[[1, :a], [2, :b]].each { |num, sym| puts "#{num} #{sym}" }
[[1, [2, 3]]].each { |a, (b, c)| puts a + b + c }
{ x: 1 }.map { |key, value| [key, value] }
```

### Splat `*` on the right-hand side or in calls

```ruby
a = [1, 2]
b = [3, 4]
[*a, *b]               # [1, 2, 3, 4]
[0, *a, 5]             # [0, 1, 2, 5]
[*1..3, *"x".."z"]     # [1, 2, 3, "x", "y", "z"]

def add(x, y, z) = x + y + z
add(*[1, 2, 3])        # 6    (expands the array into arguments)

*all = 1                # all = [1]
[*nil]                  # []
[*"text"]               # ["text"]
```

### Swapping and rotating values

```ruby
a, b = b, a
first, second, third = third, first, second
```

### Array to hash and back

```ruby
pairs = [[:a, 1], [:b, 2]]
pairs.to_h                   # {a: 1, b: 2}
{ a: 1 }.to_a                # [[:a, 1]]
```

---

[Previous](./[13]-Blocks-Procs-and-Lambdas.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[15]-Hashes.md)