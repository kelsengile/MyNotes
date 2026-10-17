[Previous](./[8]-Conditionals-And-Pattern-Matching.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[10]-Arrays.md)

*Control Flow*

# Lesson 9 - Loops And Iteration

## 9.1 while And until

```ruby
i = 0
while i < 3
  puts i
  i += 1
end

n = 3
until n.zero?
  puts n
  n -= 1
end
```

Modifier form and the do-while style (the body always runs at least once):

```ruby
i = 10
i -= 1 while i > 5

begin
  line = gets
end while line && line.strip.empty?
```

---

## 9.2 loop

`loop` repeats forever until `break` or an exception. It rescues `StopIteration` automatically.

```ruby
count = 0
loop do
  count += 1
  next if count.odd?
  break if count > 6
  puts count          # 2, 4, 6
end
```

`break` can return a value from the loop:

```ruby
result = loop do
  value = rand(10)
  break value if value > 7
end
```

---

## 9.3 for Loops (And Why Rubyists Prefer each)

```ruby
for i in 1..3
  puts i
end
```

`for` does not create a new scope: `i` is still available after the loop. Most Ruby code uses iterator methods instead.

---

## 9.4 Iterator Methods

```ruby
5.times { |i| puts i }                # 0..4
1.upto(3) { |i| puts i }              # 1, 2, 3
3.downto(1) { |i| puts i }            # 3, 2, 1
0.step(20, 5) { |i| puts i }          # 0, 5, 10, 15, 20
1.step(by: 2, to: 9) { |i| puts i }   # 1, 3, 5, 7, 9

[10, 20].each { |x| puts x }
[10, 20].each_with_index { |x, i| puts "#{i}: #{x}" }
{ a: 1, b: 2 }.each { |key, value| puts "#{key}=#{value}" }
(1..3).each { |n| puts n }
"abc".each_char { |c| puts c }
File.foreach("data.txt") { |line| puts line }
```

Blocks use either braces (for one line) or `do ... end` (for several lines):

```ruby
[1, 2, 3].each do |n|
  squared = n * n
  puts squared
end
```

Numbered parameters and the `it` parameter shorten simple blocks:

```ruby
[1, 2, 3].each { puts _1 * 2 }
[1, 2, 3].each { puts it * 2 }     # `it` is available since Ruby 3.4
```

---

## 9.5 Loop Control: break, next, redo

```ruby
(1..10).each do |n|
  next if n.even?       # skip to the next iteration
  break if n > 7        # leave the loop
  puts n                # 1, 3, 5, 7
end

found = [4, 8, 15].each do |n|
  break n if n > 5      # break returns a value
end
```

- `break` exits the loop (or block's method) and may supply a value.
- `next` jumps to the next iteration and may supply the block's return value.
- `redo` repeats the current iteration without re-checking the condition (rare).
- `retry` re-runs a `begin` block (see Lesson 17).

To exit several nested loops, use `catch`/`throw`:

```ruby
result = catch(:done) do
  (1..3).each do |i|
    (1..3).each do |j|
      throw :done, [i, j] if i * j == 6
    end
  end
  nil
end
p result   #=> [2, 3]
```

---

## 9.6 Choosing The Right Loop

| Need | Use |
| --- | --- |
| Repeat N times | `n.times` |
| Walk a collection | `each`, `each_with_index` |
| Build a new array from a collection | `map` |
| Keep matching items | `select` / `filter`, `reject` |
| Reduce to one value | `sum`, `reduce` |
| Repeat while a condition holds | `while` / `until` |
| Repeat until something inside says stop | `loop` + `break` |

Prefer the iterators for collections; they are shorter and avoid off-by-one errors. They are covered in detail in Lessons 10 to 12.

---

## 9.7 Enumerators And External Iteration

When you call an iterator method without a block, you usually get an `Enumerator`:

```ruby
e = [10, 20, 30].each
e.next      #=> 10
e.next      #=> 20
e.next      #=> 30
e.next      # raises StopIteration

%w[a b c].each_with_index.to_a     #=> [["a", 0], ["b", 1], ["c", 2]]
%w[a b c].map.with_index(1) { |ch, i| "#{i}:#{ch}" }  #=> ["1:a", "2:b", "3:c"]
```

`loop` ends silently when an enumerator raises `StopIteration`:

```ruby
e = [1, 2].each
loop { puts e.next }
```

---

[Previous](./[8]-Conditionals-And-Pattern-Matching.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[10]-Arrays.md)
