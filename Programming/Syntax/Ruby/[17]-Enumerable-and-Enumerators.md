[Previous](./[16]-Ranges-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[18]-Truthiness-nil-and-Equality.md)

*Core Syntax*

# Lesson 17 - Enumerable & Enumerators (map, select, reduce, lazy)

`Enumerable` is a **module** that gives a collection dozens of searching, filtering, transforming, and combining methods. Arrays, hashes, ranges, sets, structs, and files all use it. An **Enumerator** is an object that represents an iteration, which you can chain, step through manually, or make lazy.

---

## 17.1 The Enumerable Module

A class becomes enumerable by doing two things:

1. defining an `each` method that yields each element, and
2. including the `Enumerable` module (Lesson 25).

Everything else comes for free. Built-in classes that include it:

```ruby
[1, 2, 3].is_a?(Enumerable)     # true
{ a: 1 }.is_a?(Enumerable)      # true
(1..3).is_a?(Enumerable)        # true
Set[1].is_a?(Enumerable)        # true
Struct.new(:a).new(1).is_a?(Enumerable)   # true
File.open(__FILE__).is_a?(Enumerable)     # true (IO is enumerable by line)
```

You can list the methods:

```ruby
puts Enumerable.instance_methods.sort.first(20).inspect
```

Method names come in pairs for convenience:

| Name | Alias |
|------|-------|
| `map` | `collect` |
| `select` | `filter`, `find_all` |
| `reduce` | `inject` |
| `find` | `detect` |
| `include?` | `member?` |
| `each_entry` | |

The methods in the next sections work on every enumerable. Examples use arrays.

---

## 17.2 map, select, reject, and find

```ruby
nums = [1, 2, 3, 4, 5, 6]
```

### map: transform every element

```ruby
nums.map { |n| n * n }        # [1, 4, 9, 16, 25, 36]
nums.map(&:to_s)              # ["1", "2", ...]
{ a: 1, b: 2 }.map { |k, v| [k, v * 10] }.to_h
```

### select / filter: keep matching elements

```ruby
nums.select(&:even?)          # [2, 4, 6]
nums.filter { |n| n > 4 }     # [5, 6]
```

### reject: drop matching elements

```ruby
nums.reject(&:even?)          # [1, 3, 5]
```

### find / detect: first match (or nil)

```ruby
nums.find { |n| n > 3 }       # 4
nums.find { |n| n > 100 }     # nil
nums.find_index { |n| n > 3 } # 3     (its position)
nums.find_index(5)            # 4
```

### More filtering and mapping

```ruby
nums.filter_map { |n| n * 2 if n.even? }    # [4, 8, 12]  (map + drop nil/false)
nums.flat_map { |n| [n, -n] }.first(4)      # [1, -1, 2, -2]
nums.take(2)                                # [1, 2]
nums.drop(4)                                # [5, 6]
nums.take_while { |n| n < 3 }               # [1, 2]
nums.drop_while { |n| n < 5 }               # [5, 6]
nums.first                                  # 1
nums.first(2)                               # [1, 2]
nums.min                                    # 1
nums.max                                    # 6
nums.min_by { |n| (n - 4).abs }             # 4
nums.max_by { |n| -n }                      # 1
nums.minmax                                 # [1, 6]
nums.sort_by { |n| -n }                     # [6, 5, 4, 3, 2, 1]
nums.uniq                                   # remove duplicates
nums.zip(nums.rotate)                       # pairs with the next element
nums.count(&:even?)                         # 3
nums.sum                                    # 21
```

### Testing the collection

```ruby
nums.any? { |n| n > 5 }       # true
nums.all? { |n| n > 0 }       # true
nums.none? { |n| n > 10 }     # true
nums.one? { |n| n == 3 }      # true
nums.include?(4)              # true
nums.all?(Integer)            # true   (pattern argument uses ===)
%w[a b].any?(/b/)             # true
nums.each_entry.to_a
```

---

## 17.3 reduce / inject and sum

`reduce` boils a collection down to **one value** by repeatedly combining an accumulator with each element.

```ruby
nums = [1, 2, 3, 4]

nums.reduce(0) { |sum, n| sum + n }     # 10   (0 is the starting value)
nums.reduce { |sum, n| sum + n }        # 10   (starts with the first element)
nums.reduce(:+)                         # 10   (a symbol names the operation)
nums.reduce(1, :*)                      # 24   (starting value, then the operation)
nums.inject(:+)                         # inject is the same as reduce
```

How it works step by step for `[1, 2, 3, 4].reduce(0) { |acc, n| acc + n }`:

| Step | acc | n | result |
|------|-----|---|--------|
| 1 | 0 | 1 | 1 |
| 2 | 1 | 2 | 3 |
| 3 | 3 | 3 | 6 |
| 4 | 6 | 4 | 10 |

More examples:

```ruby
nums.reduce { |a, b| a > b ? a : b }                    # maximum
%w[a b c].reduce("") { |str, ch| str + ch.upcase }      # "ABC"
[[1, 2], [3]].reduce([], :+)                            # [1, 2, 3]
words.reduce(Hash.new(0)) { |h, w| h[w] += 1; h }       # the block must return the accumulator!
```

Forgetting to return the accumulator is the classic `reduce` bug.

### sum

`sum` is a faster, clearer shortcut for adding up:

```ruby
nums.sum                        # 10
nums.sum { |n| n * 2 }          # 20
[0.1, 0.2, 0.3].sum             # 0.6 (uses compensated summation, more accurate than +)
%w[a b].sum("")                 # "ab"  (give a starting value for non-numbers)
[[1], [2]].sum([])              # [1, 2]
```

---

## 17.4 group_by, partition, tally, and each_with_object

### group_by: split into a hash of groups

```ruby
words = %w[apple avocado banana blueberry cherry]
words.group_by { |w| w[0] }
# {"a"=>["apple", "avocado"], "b"=>["banana", "blueberry"], "c"=>["cherry"]}

(1..10).group_by { |n| n % 3 }
# {1=>[1, 4, 7, 10], 2=>[2, 5, 8], 0=>[3, 6, 9]}
```

### partition: split into two arrays

```ruby
evens, odds = (1..6).partition(&:even?)
p evens    # [2, 4, 6]
p odds     # [1, 3, 5]
```

### tally: count occurrences

```ruby
%w[a b a c b a].tally       # {"a"=>3, "b"=>2, "c"=>1}
"hello".chars.tally         # {"h"=>1, "e"=>1, "l"=>2, "o"=>1}
words.tally_by(&:size)      # count by a computed key (Ruby 3.1+)
```

### each_with_object: build something while iterating

Unlike `reduce`, you don't need to return the accumulator:

```ruby
(1..4).each_with_object({}) { |n, hash| hash[n] = n * n }
# {1=>1, 2=>4, 3=>9, 4=>16}

%w[a b].each_with_object([]) { |s, arr| arr << s.upcase }
```

### More grouping helpers

```ruby
(1..6).each_slice(2).to_a           # [[1, 2], [3, 4], [5, 6]]
(1..4).each_cons(2).to_a            # [[1, 2], [2, 3], [3, 4]]
[1, 2, 4, 9, 10, 11].chunk_while { |a, b| b == a + 1 }.to_a
# [[1, 2], [4], [9, 10, 11]]  (runs of consecutive numbers)
[1, 2, 4, 9].slice_when { |a, b| b > a + 1 }.to_a   # [[1, 2], [4], [9]]
%w[a b c].each_with_index.to_a      # [["a", 0], ["b", 1], ["c", 2]]
%w[a b c].each.with_index(1).map { |s, i| "#{i}.#{s}" }
%w[a b c].cycle.first(5)            # ["a", "b", "c", "a", "b"]
[1, 2, 3].zip([4, 5, 6], [7, 8, 9]) # [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
[[1, :a], [2, :b]].to_h
```

### A small data example

```ruby
orders = [
  { customer: "Ada", total: 30 },
  { customer: "Alan", total: 20 },
  { customer: "Ada", total: 50 }
]

orders.group_by { |o| o[:customer] }
      .transform_values { |list| list.sum { |o| o[:total] } }
# {"Ada"=>80, "Alan"=>20}
```

---

## 17.5 Enumerators and External Iteration

Most iterator methods return an **Enumerator** when called without a block:

```ruby
e = [10, 20, 30].each
puts e.class      # Enumerator
p [1, 2, 3].map   # #<Enumerator: [1, 2, 3]:map>
```

Enumerators let you chain methods:

```ruby
%w[a b c].each_with_index.map { |s, i| "#{s}#{i}" }   # ["a0", "b1", "c2"]
%w[a b c].map.with_index(1) { |s, i| "#{i}:#{s}" }    # ["1:a", "2:b", "3:c"]
[1, 2, 3].each.with_object([]) { |n, memo| memo << n * 2 }
```

### External iteration: next, peek, rewind

Normally the collection pushes values into your block (*internal iteration*). With `next`, **you pull** values one at a time (*external iteration*):

```ruby
e = [1, 2, 3].each
puts e.next    # 1
puts e.next    # 2
puts e.peek    # 3   (look without advancing)
puts e.next    # 3
# e.next       # StopIteration
e.rewind
puts e.next    # 1
```

`loop` rescues `StopIteration` for you:

```ruby
e = %w[a b c].each
loop do
  puts e.next
end
puts "done"
```

### Creating your own with Enumerator.new

The block receives a **yielder** that you push values into. The code runs only as values are requested, so infinite sequences are possible:

```ruby
fib = Enumerator.new do |y|
  a, b = 0, 1
  loop do
    y << a            # also y.yield(a)
    a, b = b, a + b
  end
end

p fib.take(10)        # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
p fib.first(5)        # [0, 1, 1, 2, 3]
p fib.lazy.select(&:even?).first(5)    # [0, 2, 8, 34, 144]
puts fib.next         # 0
puts fib.next         # 1
```

### Enumerator.produce

Generates a sequence from a starting value and a rule:

```ruby
p Enumerator.produce(1) { |n| n * 2 }.take(6)    # [1, 2, 4, 8, 16, 32]
p Enumerator.produce([0, 1]) { |a, b| [b, a + b] }.take(5).map(&:first)   # [0, 1, 1, 2, 3]
```

### Sizes

```ruby
[1, 2, 3].each.size     # 3
fib.size                # nil (unknown or infinite)
loop.size               # Infinity
```

---

## 17.6 Lazy Enumerators

A normal chain of `map`/`select` processes the **whole** collection at each step and creates intermediate arrays. A **lazy** enumerator processes **one element at a time** through the whole chain, and only as far as needed.

```ruby
(1..Float::INFINITY).lazy
  .map { |n| n * 2 }
  .select { |n| n % 3 == 0 }
  .first(3)
# [6, 12, 18]
```

Without `lazy`, that code would loop forever. With it, only as many numbers as necessary are computed.

Create one with `.lazy` on any enumerable, then **force** evaluation with a method that needs real results:

```ruby
lz = (1..20).lazy.select(&:even?).map { |n| n * n }
puts lz.class         # Enumerator::Lazy
p lz.first(3)         # [4, 16, 36]
p lz.to_a             # evaluate everything (also lz.force)
p lz.eager            # convert to a regular (non-lazy) enumerator
```

Supported lazy methods include `map`, `select`/`filter`, `reject`, `take`, `take_while`, `drop`, `drop_while`, `flat_map`, `filter_map`, `zip`, `uniq`, `with_index`, and `compact`.

### Good uses

- **Infinite sequences:** primes, Fibonacci, counters.
- **Huge files**, so lines are processed one at a time with low memory:

```ruby
File.foreach("big.log").lazy
    .select { |line| line.include?("ERROR") }
    .map(&:strip)
    .first(10)
```

- **Early exit**: stop as soon as you have enough results.

Lazy has overhead per element, so for small collections a normal chain is faster. Use `lazy` when the data is large, infinite, or expensive to compute.

```ruby
require "prime"
puts Prime.lazy.select { |p| p % 10 == 3 }.first(5).inspect   # [3, 13, 23, 43, 53]
```

---

## 17.7 Making Your Own Class Enumerable

Define `each`, include `Enumerable`, and you get all the methods in this lesson.

```ruby
class NumberCollection
  include Enumerable

  def initialize(*numbers)
    @numbers = numbers
  end

  def each
    return to_enum(:each) unless block_given?   # support calls without a block
    @numbers.each { |n| yield n }
    self
  end
end

nums = NumberCollection.new(5, 3, 8, 1)

p nums.sort                  # [1, 3, 5, 8]
p nums.map { |n| n * 2 }     # [10, 6, 16, 2]
p nums.select(&:odd?)        # [5, 3, 1]
p nums.min                   # 1
p nums.include?(8)           # true
p nums.first(2)              # [5, 3]
p nums.reduce(:+)            # 17
p nums.sort_by { |n| -n }    # [8, 5, 3, 1]
p nums.each_slice(2).to_a    # [[5, 3], [8, 1]]
p nums.lazy.map { |n| n + 1 }.first(2)   # [6, 4]
p nums.each.next             # 5
p nums.to_a                  # [5, 3, 8, 1]
p nums.each_with_index.to_a
```

Key points:

- Only `each` is required.
- `return to_enum(:each) unless block_given?` makes the object work with external iteration and chaining (Lesson 13).
- `min`, `max`, `sort`, and `minmax` rely on elements responding to `<=>`.
- For your *objects* to be comparable (so they can be sorted or `between?`), include `Comparable` and define `<=>` (Lesson 30).
- Enumerable methods return **arrays** (not your own class). Wrap results if you need an instance of your own type.

A second example, iterating a linked list:

```ruby
class LinkedList
  include Enumerable
  Node = Struct.new(:value, :next_node)

  def initialize
    @head = nil
  end

  def <<(value)
    node = Node.new(value)
    if @head.nil?
      @head = node
    else
      current = @head
      current = current.next_node while current.next_node
      current.next_node = node
    end
    self
  end

  def each
    return to_enum(:each) unless block_given?
    current = @head
    while current
      yield current.value
      current = current.next_node
    end
  end
end

list = LinkedList.new
list << 3 << 1 << 2
p list.sort       # [1, 2, 3]
p list.to_a       # [3, 1, 2]
p list.max        # 3
```

---

[Previous](./[16]-Ranges-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[18]-Truthiness-nil-and-Equality.md)