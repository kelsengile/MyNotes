[Previous](./[13]-Methods.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[15]-Classes-And-Objects.md)

*Methods And Blocks*

# Lesson 14 - Blocks Procs And Lambdas

## 14.1 What Is A Block?

A block is a chunk of code passed to a method, written with `{ ... }` or `do ... end`. The method runs it with `yield`.

```ruby
def twice
  yield
  yield
end

twice { puts "hi" }

def with_value
  result = yield 5, 10      # arguments go to the block parameters
  puts "block returned #{result}"
end

with_value { |a, b| a + b } #=> block returned 15
```

Convention: `{ }` for one-line blocks; `do ... end` for multi-line blocks. Braces bind to the nearest call, `do...end` to the outermost one, which matters in `puts [1, 2].map do |x| ... end`.

Check whether a block was given:

```ruby
def maybe
  if block_given?
    yield
  else
    "no block"
  end
end

maybe              #=> "no block"
maybe { "block" }  #=> "block"
```

A common pattern is returning an `Enumerator` when no block is given:

```ruby
def each_pair_of(list)
  return to_enum(:each_pair_of, list) unless block_given?
  list.each_cons(2) { |a, b| yield a, b }
end
```

---

## 14.2 Block Parameters

```ruby
[[1, 2], [3, 4]].each { |a, b| puts a + b }          # destructuring
{ a: 1 }.each { |key, value| puts "#{key}: #{value}" }
[1, 2, 3].each_with_index { |n, i| }
[[1, [2, 3]]].each { |a, (b, c)| puts a + b + c }    # nested
[1, 2].each { |n; temp| temp = n }                   # block-local variable temp
```

Shortcuts for simple blocks:

```ruby
[1, 2, 3].map { _1 * 2 }     # numbered parameters: _1, _2, ...
[1, 2, 3].map { it * 2 }     # `it` (Ruby 3.4+): the single implicit parameter
%w[a b].map(&:upcase)        # Symbol#to_proc
```

Blocks are lenient about arguments: missing ones are `nil` and extras are ignored.

---

## 14.3 Closures

A block remembers the variables in the place where it was created (its *binding*), even after that scope has finished.

```ruby
def counter
  count = 0
  -> { count += 1 }
end

c = counter
c.call   #=> 1
c.call   #=> 2
d = counter
d.call   #=> 1   (independent variable)
```

```ruby
multiplier = 3
[1, 2, 3].map { |n| n * multiplier }   #=> [3, 6, 9]
```

---

## 14.4 Procs

A `Proc` is a block turned into an object that can be stored, passed around, and called later.

```ruby
square = Proc.new { |x| x * x }
double = proc { |x| x * 2 }

square.call(4)    #=> 16
square.(4)        #=> 16
square[4]         #=> 16
square.yield(4)   #=> 16

def run(&block)    # & converts a block into a Proc
  block.call(10)
end
run { |n| n + 1 }  #=> 11

handler = proc { |n| n * 3 }
[1, 2].map(&handler)   # & converts a Proc back into a block
```

---

## 14.5 Lambdas

A lambda is a stricter kind of proc, created with `lambda` or the `->` literal.

```ruby
add = lambda { |a, b| a + b }
sub = ->(a, b) { a - b }
greet = ->(name = "you") { "Hi, #{name}" }

add.call(1, 2)    #=> 3
sub.(5, 3)        #=> 2
greet.()          #=> "Hi, you"
add.lambda?       #=> true
```

| | Proc | Lambda |
| --- | --- | --- |
| Argument checking | Lenient (extras ignored, missing are `nil`) | Strict (`ArgumentError`) |
| `return` | Returns from the **enclosing method** | Returns from the **lambda only** |
| Created with | `proc`, `Proc.new` | `lambda`, `->` |

```ruby
def test_proc
  p = proc { return 10 }
  p.call
  20            # never reached
end
test_proc       #=> 10

def test_lambda
  l = -> { return 10 }
  l.call
  20
end
test_lambda     #=> 20
```

In a proc use `next value` to leave just the block; calling a proc with `return` after its method has finished raises `LocalJumpError`.

---

## 14.6 Method References, Composition, And Currying

```ruby
%w[1 2 3].map(&method(:Integer))   #=> [1, 2, 3]

double = ->(x) { x * 2 }
inc = ->(x) { x + 1 }

(double >> inc).call(5)   #=> 11  (double, then inc)
(double << inc).call(5)   #=> 12  (inc, then double)
(method(:puts) << double).call(4)   # prints 8

add = ->(a, b, c) { a + b + c }
add.curry[1][2][3]        #=> 6
add5 = add.curry[2][3]
add5[10]                  #=> 15

add.arity                 #=> 3
add.parameters            #=> [[:req, :a], [:req, :b], [:req, :c]]
```

`Symbol#to_proc` and `Method#to_proc` let symbols and methods act as blocks; `Hash#to_proc` also exists: `[:a, :b].map(&{ a: 1, b: 2 })  #=> [1, 2]`.

---

## 14.7 Common Block-Based Patterns

**Resource management** (the block form cleans up automatically):

```ruby
File.open("out.txt", "w") { |f| f.puts "hi" }   # file closed afterwards
```

**Callbacks and configuration:**

```ruby
class Button
  def on_click(&handler) = @handler = handler
  def click = @handler&.call(self)
end
```

**Wrapping behavior (around):**

```ruby
def timed(label)
  start = Process.clock_gettime(Process::CLOCK_MONOTONIC)
  result = yield
  elapsed = Process.clock_gettime(Process::CLOCK_MONOTONIC) - start
  puts "#{label}: #{(elapsed * 1000).round(2)} ms"
  result
end

timed("sum") { (1..1_000_000).sum }
```

**Internal DSLs** use `instance_eval` to run a block in another object's context (see Lesson 21).

---

[Previous](./[13]-Methods.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[15]-Classes-And-Objects.md)
