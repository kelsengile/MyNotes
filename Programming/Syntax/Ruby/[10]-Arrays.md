[Previous](./[9]-Loops-And-Iteration.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[11]-Hashes.md)

*Collections*

# Lesson 10 - Arrays

## 10.1 Creating Arrays

An `Array` is an ordered, indexed, growable collection that can hold objects of any type.

```ruby
nums = [1, 2, 3]
mixed = [1, "two", :three, 4.0, nil, [5, 6]]
empty = []
words = %w[apple banana cherry]        # array of strings
syms = %i[red green blue]              # array of symbols
Array.new(3)                           #=> [nil, nil, nil]
Array.new(3, 0)                        #=> [0, 0, 0]
Array.new(3) { |i| i * i }             #=> [0, 1, 4]
Array(1..4)                            #=> [1, 2, 3, 4]
(1..4).to_a
"a b c".split                          #=> ["a", "b", "c"]
```

**Gotcha:** `Array.new(3, [])` fills the array with the *same* inner array. Use the block form `Array.new(3) { [] }` for independent rows.

---

## 10.2 Accessing Elements

```ruby
a = %w[a b c d e]

a[0]         #=> "a"
a[-1]        #=> "e"
a[1, 2]      #=> ["b", "c"]   (start, length)
a[1..3]      #=> ["b", "c", "d"]
a[3..]       #=> ["d", "e"]
a[10]        #=> nil
a.first      #=> "a"
a.first(2)   #=> ["a", "b"]
a.last(2)    #=> ["d", "e"]
a.fetch(1)   #=> "b"
a.fetch(10)  # IndexError
a.fetch(10, "none")   #=> "none"
a.values_at(0, 2, 4)  #=> ["a", "c", "e"]
a.dig(0)     #=> "a"
[[1, [2]]].dig(0, 1, 0)   #=> 2
a.sample     # random element
a.size       #=> 5 (alias: length)
a.count("a") #=> 1
```

---

## 10.3 Adding And Removing Elements

```ruby
a = [1, 2, 3]

a << 4              # [1, 2, 3, 4]   (append)
a.push(5, 6)        # [1, 2, 3, 4, 5, 6]
a.unshift(0)        # [0, 1, 2, ...] (prepend)
a.insert(2, :x)     # insert before index 2
a.pop               # removes and returns last
a.shift             # removes and returns first
a.delete(:x)        # removes all matching, returns the item
a.delete_at(1)      # removes by index
a.delete_if { |n| n > 4 }
a.compact!          # removes nils
a.concat([7, 8])    # appends another array
a.clear             # []
a[5] = 1            # pads with nil: [nil, nil, nil, nil, nil, 1]
a.fill(0)
```

Combining arrays:

```ruby
[1, 2] + [3]           #=> [1, 2, 3]
[1, 2, 3] - [2]        #=> [1, 3]
[1, 2] * 2             #=> [1, 2, 1, 2]
[1, 2] * ", "          #=> "1, 2"
[1, 2] | [2, 3]        #=> [1, 2, 3]
[1, 2] & [2, 3]        #=> [2]
```

---

## 10.4 Iterating And Transforming

```ruby
nums = [1, 2, 3, 4, 5]

nums.each { |n| puts n }
nums.each_with_index { |n, i| puts "#{i}: #{n}" }
nums.map { |n| n * 2 }                #=> [2, 4, 6, 8, 10]
nums.select(&:even?)                  #=> [2, 4]
nums.reject(&:even?)                  #=> [1, 3, 5]
nums.partition(&:even?)               #=> [[2, 4], [1, 3, 5]]
nums.find { |n| n > 2 }               #=> 3
nums.find_index(3)                    #=> 2
nums.take(2)                          #=> [1, 2]
nums.drop(2)                          #=> [3, 4, 5]
nums.take_while { |n| n < 3 }         #=> [1, 2]
nums.each_slice(2).to_a               #=> [[1, 2], [3, 4], [5]]
nums.each_cons(2).to_a                #=> [[1, 2], [2, 3], [3, 4], [4, 5]]
nums.zip(%w[a b c d e])               #=> [[1, "a"], [2, "b"], ...]
nums.each_with_object([]) { |n, acc| acc << n * n }
nums.group_by(&:odd?)                 #=> {true => [1, 3, 5], false => [2, 4]}
nums.reduce(:+)                       #=> 15
nums.inject { |sum, n| sum * n }      #=> 120
nums.sum                              #=> 15
nums.min                              #=> 1
nums.max(2)                           #=> [5, 4]
nums.minmax                           #=> [1, 5]
nums.tally                            #=> {1 => 1, 2 => 1, ...}
nums.cycle.first(7)                   #=> [1, 2, 3, 4, 5, 1, 2]
```

---

## 10.5 Searching, Sorting, And Querying

```ruby
a = [5, 3, 8, 1]

a.include?(3)        #=> true
a.index(8)           #=> 2
a.any? { |n| n > 7 } #=> true
a.all?(Integer)      #=> true
a.none?(&:zero?)     #=> true
a.empty?             #=> false

a.sort               #=> [1, 3, 5, 8]
a.sort { |x, y| y <=> x }          #=> [8, 5, 3, 1]
a.sort_by { |n| -n }               #=> [8, 5, 3, 1]
a.reverse            #=> [1, 8, 3, 5]
a.rotate             #=> [3, 8, 1, 5]
a.shuffle
a.uniq
[3, 1, 3].uniq       #=> [3, 1]
[[1, 2], [3, [4]]].flatten      #=> [1, 2, 3, 4]
[1, nil, 2].compact             #=> [1, 2]
words = %w[pear fig apple]
words.sort_by(&:length)         #=> ["fig", "pear", "apple"]
words.max_by(&:length)          #=> "apple"
words.min_by(&:length)          #=> "fig"
words.sum("")                   #=> "pearfigapple"
words.join(", ")                #=> "pear, fig, apple"
```

Combinatorics:

```ruby
[1, 2, 3].combination(2).to_a   #=> [[1, 2], [1, 3], [2, 3]]
[1, 2, 3].permutation(2).count  #=> 6
[1, 2].product([3, 4])          #=> [[1, 3], [1, 4], [2, 3], [2, 4]]
[[1, 2], [3, 4]].transpose      #=> [[1, 3], [2, 4]]
```

---

## 10.6 Destructuring And Splat

```ruby
first, second = [10, 20, 30]      # first=10, second=20
head, *tail = [1, 2, 3]           # tail=[2, 3]
*body, last = [1, 2, 3]           # body=[1, 2], last=3

def sum(*nums) = nums.sum         # collect arguments
sum(*[1, 2, 3])                   # spread an array into arguments

[1, *[2, 3], 4]                   #=> [1, 2, 3, 4]

pairs = [[1, :a], [2, :b]]
pairs.each { |num, sym| puts "#{num}-#{sym}" }
pairs.each { |(num, sym)| puts num }   # explicit destructuring
pairs.to_h                        #=> {1 => :a, 2 => :b}
```

---

## 10.7 Mutation, Copying, And Bang Methods

Methods ending in `!` change the array in place; the regular versions return a new array.

```ruby
a = [3, 1, 2]
a.sort            # returns new array; a is unchanged
a.sort!           # a is now [1, 2, 3]
a.map!  { |n| n * 2 }
a.select! { |n| n > 2 }
a.reject! { |n| n > 100 }    # returns nil if nothing changed!
```

`dup` makes a shallow copy; use `Marshal.load(Marshal.dump(a))` for a deep copy:

```ruby
a = [[1], [2]]
b = a.dup
b[0] << 99
a            #=> [[1, 99], [2]]  (inner arrays are shared)
```

Frozen arrays raise `FrozenError` on modification:

```ruby
CONST = [1, 2].freeze
CONST << 3        # FrozenError
```

Pack and unpack convert arrays to and from binary strings: `[65, 66].pack("C*")  #=> "AB"`.

---

[Previous](./[9]-Loops-And-Iteration.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[11]-Hashes.md)
