[Previous](./[12]-Enumerable-And-Iterators.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[14]-Blocks-Procs-And-Lambdas.md)

*Methods And Blocks*

# Lesson 13 - Methods

## 13.1 Defining And Calling Methods

```ruby
def greet(name)
  "Hello, #{name}!"
end

puts greet("Ada")      #=> Hello, Ada!
puts greet "Bo"        # parentheses are optional
```

- A method name uses `snake_case`; it may end in `?`, `!`, or `=`.
- The method returns the value of its **last expression**; `return` is only needed for early exits.
- `def` returns the method name as a symbol, which allows `private def helper ... end`.
- Methods defined at the top level become private methods of `Object`, available everywhere.

Endless method definition (Ruby 3.0+) for one-expression methods:

```ruby
def square(x) = x * x
def full_name(first, last) = "#{first} #{last}"
```

---

## 13.2 Parameters

```ruby
# Required and optional (default) parameters
def power(base, exp = 2)
  base**exp
end
power(3)      #=> 9
power(2, 10)  #=> 1024

# Splat: collects extra arguments into an array
def total(*numbers)
  numbers.sum
end
total(1, 2, 3)   #=> 6

# Keyword arguments
def connect(host:, port: 80, ssl: false)
  "#{ssl ? 'https' : 'http'}://#{host}:#{port}"
end
connect(host: "example.com", ssl: true)
connect(port: 8080, host: "x.org")        # order does not matter
connect(host: "x")                         # port defaults to 80
connect                                    # ArgumentError: missing keyword: :host

# Double splat: collects extra keywords into a hash
def log(message, **meta)
  puts "#{message} #{meta}"
end
log("saved", user: 1, id: 7)

# Block parameter
def run(&block) = block.call
```

Order of parameter kinds: required, optional, `*splat`, required-after-splat, keywords, `**opts`, `&block`.

```ruby
def mix(a, b = 2, *rest, c, d:, e: 5, **others, &blk); end
```

Passing arguments:

```ruby
args = [1, 2, 3]
total(*args)                  # spread an array
opts = { host: "a.com" }
connect(**opts)               # spread a hash into keywords
x, y = 1, 2
def point(x:, y:) = [x, y]
point(x:, y:)                 # shorthand when local names match the keywords
```

Arity and strictness: passing the wrong number of arguments raises `ArgumentError`.

---

## 13.3 Argument Forwarding

```ruby
def wrapper(*args, **kwargs, &block)
  target(*args, **kwargs, &block)
end

# Short form: forward everything
def wrapper2(...)
  target(...)
end

# Anonymous forwarding (Ruby 3.2+)
def a(*, **, &) = b(*, **, &)
```

---

## 13.4 Return Values

```ruby
def min_max(list)
  [list.min, list.max]       # return several values as an array
end

low, high = min_max([3, 9, 1])

def check(n)
  return "negative" if n < 0
  return "zero" if n.zero?
  "positive"
end
```

Methods with no explicit value return `nil` (for example, one that ends with `puts`).

Naming conventions with return values:

```ruby
def empty? = @items.empty?    # predicate: returns true/false
def sort!                     # bang: modifies the receiver, often raises or returns nil on no-op
def name=(value)              # setter: used as obj.name = "x"
```

---

## 13.5 Method Visibility And Lookup At The Top Level

Inside classes, visibility keywords control who can call a method (full details in Lesson 15):

```ruby
class Account
  def public_method; end
  private
  def secret; end
end
```

At the top level, `def` creates a private method on `Object`, so you can call it from anywhere without a receiver, but not as `5.my_method`.

---

## 13.6 Recursion

```ruby
def factorial(n)
  n <= 1 ? 1 : n * factorial(n - 1)
end
factorial(5)   #=> 120

def fib(n, memo = {})
  return n if n < 2
  memo[n] ||= fib(n - 1, memo) + fib(n - 2, memo)
end
fib(50)        #=> 12586269025
```

Ruby does not optimize tail calls by default, and very deep recursion raises `SystemStackError`. Prefer iteration for large inputs.

---

## 13.7 Method Objects, Aliases, And Introspection

```ruby
m = 5.method(:+)
m.call(3)             #=> 8
m.owner               #=> Integer
m.arity               #=> 1
[1, 2, 3].map(&10.method(:+))   #=> [11, 12, 13]

um = Array.instance_method(:size)   # unbound method
um.bind([1, 2]).call                #=> 2

class Greeter
  def hello = "hi"
  alias_method :hi, :hello     # method form (used inside classes and modules)
  alias hey hello              # keyword form
end

Greeter.new.hey                       #=> "hi"
Greeter.instance_method(:hello).source_location   #=> ["file.rb", 2]
Greeter.new.respond_to?(:hello)       #=> true
Greeter.new.respond_to?(:secret, true)  # true as 2nd argument also checks private methods
```

Inside a method, `__method__` returns the current method's name as a symbol.

`Kernel#send` calls any method by name, including private ones; `public_send` respects visibility.

```ruby
"abc".send(:upcase)          #=> "ABC"
"abc".public_send(:length)   #=> 3
```

Use `undef_method` or `remove_method` to delete a method in a class.

---

[Previous](./[12]-Enumerable-And-Iterators.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[14]-Blocks-Procs-And-Lambdas.md)
