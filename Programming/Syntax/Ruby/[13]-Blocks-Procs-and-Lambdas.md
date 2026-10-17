[Previous](./[12]-Methods.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[14]-Arrays.md)

*Core Syntax*

# Lesson 13 - Blocks, Procs & Lambdas (yield, closures, &:symbol)

Blocks are chunks of code you hand to a method. They are the foundation of Ruby's iterators, callbacks, and domain-specific languages. Procs and lambdas turn that code into objects you can store and pass around.

---

## 13.1 What Is a Block?

A **block** is an anonymous piece of code attached to a method call, written with `{ }` or `do ... end`.

```ruby
[1, 2, 3].each { |n| puts n * 2 }

[1, 2, 3].each do |n|
  puts n * 2
end
```

- The parameters between the pipes `|n|` receive the values the method passes in.
- Convention: **`{ }` for one-liners**, **`do...end` for multi-line** blocks.
- The two forms differ in precedence. `do...end` binds to the **leftmost** method call, `{ }` to the nearest:

```ruby
puts [1, 2, 3].map { |n| n * 2 }       # block goes to map: prints 2 4 6
puts [1, 2, 3].map do |n| n * 2 end    # block goes to puts: prints an enumerator
```

- Blocks are **not objects** by themselves, and a method receives at most one block.
- Blocks can have several parameters and destructure values:

```ruby
{ a: 1, b: 2 }.each { |key, value| puts "#{key}=#{value}" }
[[1, 2], [3, 4]].each { |(a, b)| puts a + b }
[1, 2, 3].each_with_index { |n, i| puts "#{i}: #{n}" }
```

- Block parameters you don't need can be skipped with `_`.
- Blocks without declared parameters can use **numbered parameters** `_1`, `_2`, or (Ruby 3.4+) the shorthand **`it`**:

```ruby
[1, 2, 3].map { _1 * 2 }       # [2, 4, 6]
[1, 2, 3].map { it * 2 }       # [2, 4, 6]  (Ruby 3.4+)
```

---

## 13.2 yield and block_given?

To write a method that accepts a block, call **`yield`**. It runs the block and passes it any arguments:

```ruby
def twice
  yield
  yield
end

twice { puts "Hello!" }
# Hello!
# Hello!
```

Passing values to the block, and using the block's return value:

```ruby
def transform(value)
  result = yield(value)     # the block's last expression is yield's value
  "Result: #{result}"
end

puts transform(5) { |n| n * 10 }     # Result: 50
puts transform("hi") { |s| s.upcase } # Result: HI
```

Building your own `each` and `map`:

```ruby
def my_each(array)
  i = 0
  while i < array.length
    yield array[i]
    i += 1
  end
  array
end

def my_map(array)
  result = []
  my_each(array) { |item| result << yield(item) }
  result
end

p my_map([1, 2, 3]) { |n| n * n }    # [1, 4, 9]
```

### block_given?

Calling `yield` without a block raises `LocalJumpError`. Check first when the block is optional:

```ruby
def greet(name)
  if block_given?
    yield(name)
  else
    "Hello, #{name}!"
  end
end

puts greet("Ada")                       # Hello, Ada!
puts greet("Ada") { |n| "Yo, #{n}" }    # Yo, Ada
```

### Capturing the block with &block

An `&` parameter turns the block into a `Proc` object, so you can store it or pass it on:

```ruby
def run(&block)
  puts block.class      # Proc
  block.call(3)
end

run { |x| puts x * 2 }

def each_twice(items, &block)
  items.each(&block)     # pass the block along
  items.each(&block)
end
```

### Returning an enumerator when no block is given

A common idiom for iterator methods:

```ruby
def countdown(from)
  return to_enum(:countdown, from) unless block_given?
  from.downto(1) { |n| yield n }
end

countdown(3) { |n| puts n }
p countdown(3).to_a      # [3, 2, 1]
```

---

## 13.3 Procs and Lambdas

A **Proc** is a block turned into an object. A **lambda** is a special, stricter kind of Proc.

```ruby
# Proc
square = Proc.new { |x| x * x }
square2 = proc { |x| x * x }

# Lambda
cube = lambda { |x| x ** 3 }
cube2 = ->(x) { x ** 3 }          # "stabby lambda" literal (most common)

puts square.call(4)    # 16
puts cube.call(3)      # 27
```

Ways to call them:

```ruby
cube.call(2)
cube.(2)
cube[2]
cube.yield(2)
```

Multi-line lambda and default arguments:

```ruby
greet = ->(name, greeting = "Hello") do
  "#{greeting}, #{name}!"
end
puts greet.("Ada")
```

Storing and passing behavior:

```ruby
operations = {
  add: ->(a, b) { a + b },
  mul: ->(a, b) { a * b }
}
puts operations[:add].call(2, 3)    # 5
puts operations[:mul].(2, 3)        # 6

def apply(value, fn)
  fn.call(value)
end
puts apply(5, ->(x) { x + 1 })      # 6
```

Pass a Proc or lambda to a method **as a block** with `&`:

```ruby
double = ->(x) { x * 2 }
p [1, 2, 3].map(&double)      # [2, 4, 6]
```

---

## 13.4 Proc vs Lambda Differences

Both are `Proc` objects, but they behave differently in two ways.

### 1. Argument checking

A lambda is strict, like a method. A proc is relaxed:

```ruby
l = ->(a, b) { "#{a} #{b}" }
pr = proc { |a, b| "#{a.inspect} #{b.inspect}" }

# l.call(1)              # ArgumentError: wrong number of arguments (given 1, expected 2)
puts pr.call(1)          # 1 nil        (missing args become nil)
puts pr.call(1, 2, 3)    # 1 2          (extra args are dropped)
puts pr.call([5, 6])     # 5 6          (an array argument is unpacked)
```

### 2. What `return` does

- In a **lambda**, `return` exits just the lambda (like a method).
- In a **proc**, `return` exits the **enclosing method**.

```ruby
def lambda_test
  l = -> { return 1 }
  l.call
  "lambda_test finished"
end
puts lambda_test      # lambda_test finished

def proc_test
  pr = proc { return 1 }
  pr.call
  "proc_test finished"      # never reached
end
puts proc_test        # 1
```

A proc called after its method already ended, or at top level, raises `LocalJumpError` when it uses `return`. The same applies to `break`. `next` in either returns from the proc or lambda itself.

### Checking

```ruby
puts l.lambda?     # true
puts pr.lambda?    # false
p l.arity          # 2
p pr.arity         # 2
p l.parameters     # [[:req, :a], [:req, :b]]
```

### Which should I use?

| Use | When |
|-----|------|
| **Block** | Passing code to one method call |
| **Lambda** | A reusable, method-like function with strict arguments (usually the right choice for stored callables) |
| **Proc** | Rarely. Mostly for behavior that needs relaxed argument handling |

---

## 13.5 Closures and Variable Scope

Blocks, procs, and lambdas are **closures**: they remember the variables that were in scope where they were *created*, even after that scope has finished.

```ruby
def make_counter
  count = 0
  -> { count += 1 }
end

counter = make_counter
puts counter.call    # 1
puts counter.call    # 2
puts counter.call    # 3

other = make_counter
puts other.call      # 1  (its own separate count)
```

`count` lives on inside the lambda. Another example, a function factory:

```ruby
def multiplier(factor)
  ->(x) { x * factor }
end

double = multiplier(2)
triple = multiplier(3)
puts double.call(5)    # 10
puts triple.call(5)    # 15
```

Closures see **current** values of outer variables, not copies:

```ruby
x = 10
show = -> { puts x }
x = 20
show.call            # 20
```

Contrast with `def`, which cannot see outer locals (Lesson 5).

### Block-local variables

Parameters and variables defined inside a block disappear after it. To prevent a block from changing an outer variable of the same name, declare a **block-local** variable after a semicolon:

```ruby
x = 5
[1, 2].each { |n; x| x = n * 10 }   # this x is a separate variable
puts x                               # 5
```

### `binding`

A closure's variables are stored in a **binding**. `binding` returns the current one, which can be used with `eval` or `local_variable_get` (Lesson 36):

```ruby
secret = 42
b = binding
puts b.local_variable_get(:secret)    # 42
```

---

## 13.6 Symbol to Proc (&:name) and Method Objects

### Symbol to proc

`&:method_name` is a shortcut for a block that calls that method on each item:

```ruby
%w[apple banana].map(&:upcase)       # same as map { |s| s.upcase }
[1, 2, 3].map(&:to_s)                # ["1", "2", "3"]
[1, 2, 3, 4].select(&:even?)         # [2, 4]
[[1, 2], [3]].map(&:sum)             # [3, 3]
%w[a b].each_with_index.map { |s, i| "#{i}#{s}" }
[1, 2, 3].reduce(:+)                 # 6   (reduce also accepts a bare symbol)
```

How it works: `&` calls `to_proc` on the symbol, and `Symbol#to_proc` returns a proc that calls the method named by the symbol.

```ruby
upcaser = :upcase.to_proc
puts upcaser.call("hello")    # HELLO
```

It works for methods that take **no extra arguments**. Anything more needs a regular block.

### Method objects

`method(:name)` wraps a method as an object you can store, pass around, and call:

```ruby
def double(x) = x * 2

m = method(:double)
puts m.call(4)               # 8
puts m.class                 # Method
p [1, 2, 3].map(&m)          # [2, 4, 6]   (& converts the Method to a block)

[1, 2, 3].each(&method(:puts))   # puts each number
puts 5.method(:+).call(3)        # 8
```

Useful members:

```ruby
m.arity          # 1
m.name           # :double
m.owner          # the class/module where it is defined
m.receiver       # the object it is bound to
m.unbind         # an UnboundMethod, which can be rebound with bind(obj)
m.to_proc        # a lambda
```

---

## 13.7 Currying and Composition

### Currying

**Currying** turns a function that takes several arguments into a chain of functions that each take one. Provide some arguments now and the rest later:

```ruby
add = ->(a, b, c) { a + b + c }
curried = add.curry

puts curried[1][2][3]       # 6
puts curried.(1, 2).(3)     # 6
puts curried.(1).(2, 3)     # 6

add_five = curried[2][3]    # remembers 2 and 3
puts add_five.call(10)      # 15
```

A practical example:

```ruby
power = ->(exp, base) { base ** exp }
square = power.curry[2]
cube   = power.curry[3]
puts square.call(4)   # 16
puts cube.call(2)     # 8
```

For a variable-arity lambda or proc, give the arity: `proc.curry(3)`.

### Composition

`>>` and `<<` combine callables into a pipeline:

- `f >> g` means "do `f` first, then `g`": `g(f(x))`.
- `f << g` means "do `g` first, then `f`": `f(g(x))`.

```ruby
add_one = ->(x) { x + 1 }
double  = ->(x) { x * 2 }

puts (add_one >> double).call(3)   # (3 + 1) * 2 = 8
puts (add_one << double).call(3)   # (3 * 2) + 1 = 7

clean = :strip.to_proc >> :downcase.to_proc >> :capitalize.to_proc
puts clean.call("  hELLO ")       # Hello
```

It works with Method objects too:

```ruby
pipeline = method(:Integer) >> ->(n) { n * 2 }
puts pipeline.call("21")           # 42
```

Composition makes small, single-purpose functions easy to combine, which is a core idea of functional programming style in Ruby.

---

[Previous](./[12]-Methods.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[14]-Arrays.md)