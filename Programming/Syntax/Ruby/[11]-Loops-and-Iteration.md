[Previous](./[10]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[12]-Methods.md)

*Core Syntax*

# Lesson 11 - Loops & Iteration (while, until, for, loop, times, each)

Loops repeat code. Ruby offers traditional loops (`while`, `until`, `for`, `loop`) and **iterators** (`times`, `each`, and friends), which are what most Ruby code uses.

---

## 11.1 while and until

### while

Repeats **as long as** the condition is true.

```ruby
count = 1
while count <= 3
  puts "Count: #{count}"
  count += 1
end
# Count: 1
# Count: 2
# Count: 3
```

### until

Repeats **as long as** the condition is false (until it becomes true).

```ruby
n = 3
until n.zero?
  puts n
  n -= 1
end
# 3 2 1
```

### Modifier forms

```ruby
i = 0
i += 1 while i < 10
puts i            # 10
```

### do-while behavior

Placing `while` after a `begin...end` block runs the body **at least once**:

```ruby
attempts = 0
begin
  attempts += 1
  puts "Attempt #{attempts}"
end while attempts < 3
```

### Reading until input ends

```ruby
while (line = gets)
  puts line.chomp.upcase
end
```

`while` and `until` loops return `nil` (unless you use `break` with a value).

---

## 11.2 The for Loop

`for` iterates over anything that responds to `each`:

```ruby
for i in 1..3
  puts i
end

for fruit in %w[apple banana cherry]
  puts fruit
end

for key, value in { a: 1, b: 2 }
  puts "#{key}=#{value}"
end
```

`for` is rarely used in Ruby because it has a quirk: **it does not create a new variable scope**. The loop variable is still visible afterwards:

```ruby
for i in 1..3
  inner = i * 10
end
puts i        # 3
puts inner    # 30
```

Behind the scenes `for` just calls `each`, so prefer calling `each` directly (see 11.6).

---

## 11.3 loop do

`loop` runs its block forever until you stop it with `break` (or an exception).

```ruby
count = 0
loop do
  count += 1
  next if count.odd?        # skip odd numbers
  puts count
  break if count >= 6
end
# 2 4 6
```

`break` can return a value from the loop:

```ruby
result = loop do
  value = rand(1..10)
  break value if value > 8
end
puts "Got #{result}"
```

`loop` also quietly stops when a `StopIteration` error is raised, which is how external enumerators finish (Lesson 17):

```ruby
numbers = [1, 2, 3].each
loop do
  puts numbers.next    # when the array is exhausted, loop ends cleanly
end
```

Use `loop do` for menus, retry logic, servers, and "run until some condition happens inside" patterns.

---

## 11.4 times, upto, downto, and step

Numbers are objects with their own iteration methods.

```ruby
3.times { |i| puts "Run #{i}" }          # i is 0, 1, 2
5.times { print "*" }                     # *****

1.upto(4) { |i| puts i }                  # 1 2 3 4
4.downto(1) { |i| puts i }                # 4 3 2 1
```

### step

```ruby
1.step(10, 3) { |i| puts i }              # 1 4 7 10  (from, limit, step size)
10.step(1, -4) { |i| puts i }             # 10 6 2
1.step(by: 2, to: 9) { |i| puts i }       # 1 3 5 7 9  (keyword form)
0.0.step(1.0, 0.25) { |f| puts f }        # 0.0 0.25 0.5 0.75 1.0
```

Ranges have `step` and `%` too:

```ruby
(1..10).step(3) { |i| puts i }            # 1 4 7 10
((1..10) % 3).to_a                        # [1, 4, 7, 10]
```

Without a block these methods return an **enumerator**, which you can chain:

```ruby
5.times.map { |i| i * i }                 # [0, 1, 4, 9, 16]
1.upto(5).select(&:even?)                 # [2, 4]
```

---

## 11.5 break, next, and redo

### break: leave the loop

```ruby
[1, 2, 3, 4, 5].each do |n|
  break if n > 3
  puts n
end
# 1 2 3
```

`break` can pass back a value:

```ruby
found = [4, 8, 15, 16].each do |n|
  break n if n > 10
end
puts found       # 15
```

### next: skip to the next iteration

```ruby
(1..6).each do |n|
  next if n.even?
  puts n
end
# 1 3 5
```

In methods like `map`, `next` provides the value for that iteration:

```ruby
squares = [1, 2, 3, 4].map do |n|
  next 0 if n.odd?    # use 0 for odd numbers
  n * n
end
p squares          # [0, 4, 0, 16]
```

### redo: repeat this iteration

`redo` restarts the current iteration without re-checking the condition. It is rarely used and easy to turn into an infinite loop:

```ruby
tries = 0
[1, 2].each do |n|
  tries += 1
  redo if tries == 1      # run the first item twice
  puts n
end
```

### Leaving nested loops

Ruby has no labeled `break`. Options:

```ruby
# 1. Use a method and return
def find_pair(rows)
  rows.each do |row|
    row.each do |value|
      return value if value > 10
    end
  end
  nil
end

# 2. Use catch/throw (Lesson 20)
catch(:done) do
  (1..3).each do |i|
    (1..3).each do |j|
      throw :done if i * j == 4
      puts "#{i}*#{j}"
    end
  end
end
```

(`retry`, which restarts a `begin/rescue` block, is a different feature: Lesson 20.)

---

## 11.6 Why Rubyists Prefer each

`each` is the standard way to loop over a collection:

```ruby
fruits = %w[apple banana cherry]

fruits.each do |fruit|
  puts fruit
end

fruits.each_with_index do |fruit, index|
  puts "#{index + 1}. #{fruit}"
end

{ a: 1, b: 2 }.each { |key, value| puts "#{key}: #{value}" }
(1..3).each { |n| puts n }
```

Why it is preferred over `for` and index loops:

1. **No index bookkeeping.** No `i`, no off-by-one mistakes, no `array[i]`.
2. **Works on anything enumerable.** Arrays, hashes, ranges, sets, files (`File.foreach`), custom classes.
3. **Cleaner scope.** The block variable does not leak out of the loop.
4. **Richer relatives.** Specialized iterators say *what* you want:

```ruby
numbers = [1, 2, 3, 4, 5]

numbers.each { |n| puts n }                  # do something with each
numbers.map { |n| n * 2 }                    # transform: [2, 4, 6, 8, 10]
numbers.select(&:even?)                      # filter: [2, 4]
numbers.reduce(:+)                           # combine: 15
numbers.each_slice(2).to_a                   # [[1, 2], [3, 4], [5]]
numbers.each_cons(2).to_a                    # [[1, 2], [2, 3], [3, 4], [4, 5]]
numbers.each_with_object([]) { |n, acc| acc << n * n }
"abc".each_char { |c| puts c }
File.foreach("data.txt") { |line| puts line }   # one line at a time, memory-friendly
```

Compare an index-style loop with the Ruby way:

```ruby
# Not idiomatic
i = 0
while i < numbers.length
  puts numbers[i]
  i += 1
end

# Idiomatic
numbers.each { |n| puts n }
```

You will learn how blocks work in Lesson 13 and the full set of iteration tools in Lesson 17. Use `while`/`until` only when you don't have a collection to iterate and the number of repetitions depends on a changing condition.

---

[Previous](./[10]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[12]-Methods.md)