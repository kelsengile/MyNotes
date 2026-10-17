[Previous](./[11]-Hashes.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[13]-Methods.md)

*Collections*

# Lesson 12 - Enumerable And Iterators

## 12.1 The Enumerable Module

`Enumerable` is a mixin that gives a class dozens of collection methods. `Array`, `Hash`, `Range`, `Set`, `Struct`, and `Enumerator` all include it. Your own class gets the same powers by defining `each` and including the module:

```ruby
class NumberBag
  include Enumerable

  def initialize(*nums)
    @nums = nums
  end

  def each(&block)
    return to_enum(:each) unless block_given?
    @nums.each(&block)
    self
  end
end

bag = NumberBag.new(3, 1, 2)
bag.sort                #=> [1, 2, 3]
bag.map { |n| n * 2 }   #=> [6, 2, 4]
bag.select(&:odd?)      #=> [3, 1]
bag.min                 #=> 1
bag.include?(2)         #=> true
bag.first(2)            #=> [3, 1]
bag.each_slice(2).to_a  #=> [[3, 1], [2]]
bag.reduce(:+)          #=> 6
bag.sum                 #=> 6
bag.sort_by { |n| -n }  #=> [3, 2, 1]
```

---

## 12.2 Map, Select, Reduce, And Friends

```ruby
nums = [1, 2, 3, 4, 5, 6]

nums.map { |n| n * n }              # transform every element
nums.flat_map { |n| [n, -n] }       # map, then flatten one level
nums.select { |n| n.even? }         # keep matching (alias: filter)
nums.reject { |n| n.even? }         # drop matching
nums.filter_map { |n| n * 2 if n.odd? }   #=> [2, 6, 10]
nums.find { |n| n > 3 }             # first match (alias: detect)
nums.find_all(&:even?)
nums.count(&:even?)
nums.sum { |n| n * 10 }
nums.reduce(0) { |acc, n| acc + n }
nums.reduce(:*)                      #=> 720
nums.inject(10) { |acc, n| acc - n }
nums.each_with_object({}) { |n, h| h[n] = n.to_s }
nums.group_by { |n| n % 3 }
nums.partition { |n| n > 3 }
nums.chunk_while { |a, b| b == a + 1 }.to_a   #=> [[1, 2, 3, 4, 5, 6]]
nums.slice_when { |a, b| b.odd? }.to_a
nums.each_cons(3).map(&:sum)         # sliding window
nums.min_by { |n| (n - 4).abs }      #=> 4
nums.max(2)                          #=> [6, 5]
nums.sort_by { |n| [-n % 2, n] }
nums.zip(nums.rotate)
nums.take_while { |n| n < 4 }
nums.drop_while { |n| n < 4 }
nums.all? { |n| n > 0 }
nums.any?(&:zero?)
nums.none?
nums.one? { |n| n == 6 }
nums.tally_by(&:even?)              #=> {false => 3, true => 3}
nums.each_entry { |n| }
nums.lazy.select(&:even?).map { |n| n * 3 }.first(2)  #=> [6, 12]
nums.to_set
nums.sum.fdiv(nums.size)            # average
```

`map` returns a new array; `each` returns the receiver. Chain methods to build readable pipelines:

```ruby
words = %w[apple Banana cherry date Elder]
words
  .select { |w| w.length > 4 }
  .map(&:downcase)
  .sort
  .each_with_index
  .map { |w, i| "#{i + 1}. #{w}" }
```

---

## 12.3 Enumerators

An `Enumerator` is an object that produces values on demand. Many methods return one when called without a block.

```ruby
e = [1, 2, 3].each
e.next      #=> 1
e.peek      #=> 2
e.next      #=> 2
e.rewind

[4, 5].each.with_index(1).to_a        #=> [[4, 1], [5, 2]]
[4, 5].map.with_index { |x, i| x * i }  #=> [0, 5]
"abc".each_char.with_object([]) { |c, acc| acc << c.ord }
```

Create your own with `Enumerator.new`:

```ruby
fib = Enumerator.new do |y|
  a, b = 0, 1
  loop do
    y << a
    a, b = b, a + b
  end
end

fib.take(10)          #=> [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
fib.lazy.select(&:even?).first(5)
fib.each_with_index.first(3)
```

Other generators: `Enumerator.produce(1) { |x| x * 2 }.take(5)  #=> [1, 2, 4, 8, 16]`.

---

## 12.4 Lazy Enumeration

`.lazy` processes items one at a time and stops as soon as the result is satisfied. It is essential for infinite or huge sequences.

```ruby
(1..Float::INFINITY)
  .lazy
  .select { |n| n % 3 == 0 }
  .map { |n| n * n }
  .reject(&:even?)
  .first(3)                   #=> [9, 81, 225]

File.foreach("big.log").lazy.grep(/ERROR/).map(&:strip).first(10)
```

Without `lazy`, each step would build a full intermediate array (and the infinite range would never finish). Call `.force` or `.to_a` on a lazy enumerator to evaluate it fully, and `.eager` to return to normal enumeration.

---

## 12.5 Comparable And Sorting Custom Objects

`Comparable` provides `<`, `<=`, `==`, `>`, `>=`, `between?`, and `clamp` based on one method, `<=>`:

```ruby
class Version
  include Comparable
  attr_reader :parts

  def initialize(str)
    @parts = str.split(".").map(&:to_i)
  end

  def <=>(other)
    parts <=> other.parts
  end

  def to_s = parts.join(".")
end

a = Version.new("1.2.10")
b = Version.new("1.10.0")
a < b                         #=> true
[b, a].sort.map(&:to_s)       #=> ["1.2.10", "1.10.0"]
[a, b].max.to_s               #=> "1.10.0"
a.between?(a, b)              #=> true
```

`sort_by` with an array key sorts by several criteria:

```ruby
people.sort_by { |p| [-p.age, p.name] }    # age descending, then name ascending
```

---

## 12.6 Sets

A `Set` is an unordered collection of **unique** values with fast membership tests. `Set` is available without `require` in modern Ruby (it is autoloaded, and part of the core in Ruby 4.0; older code uses `require "set"`).

```ruby
require "set"           # harmless in all versions

s = Set.new([1, 2, 3, 2])
s                       #=> #<Set: {1, 2, 3}>
s << 4
s.add?(4)               #=> nil (already present)
s.include?(2)           #=> true
s.delete(1)
s | Set[5]              # union
s & Set[2, 3]           # intersection
s - Set[2]              # difference
s ^ Set[2, 9]           # symmetric difference
s.subset?(Set[2, 3, 4, 5])
s.superset?(Set[2])
s.disjoint?(Set[9])
%w[a b a].to_set        #=> #<Set: {"a", "b"}>
s.map { |x| x * 2 }     # returns an Array
```

Use `Set` instead of `Array#include?` when checking membership in a large collection; it is O(1) on average rather than O(n).

---

[Previous](./[11]-Hashes.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[13]-Methods.md)
