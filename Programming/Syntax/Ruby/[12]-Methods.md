[Previous](./[11]-Loops-and-Iteration.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[13]-Blocks-Procs-and-Lambdas.md)

*Core Syntax*

# Lesson 12 - Methods (arguments, keyword arguments, return values)

A **method** is a named, reusable block of code. In Ruby, you ask an object to do something by calling one of its methods. Even `puts` and `+` are methods.

---

## 12.1 Defining and Calling Methods

```ruby
def greet(name)
  puts "Hello, #{name}!"
end

greet("Ada")      # Hello, Ada!
greet "Grace"     # parentheses are optional on calls
```

Anatomy:

```ruby
def method_name(parameter1, parameter2)
  # body
end
```

- Names use `snake_case`.
- **Parameters** are the names in the definition. **Arguments** are the values passed when calling.
- A method must be defined before the line that calls it runs.
- A method with no parameters needs no parentheses:

```ruby
def hello
  "Hello!"
end
puts hello
```

- Calling with the wrong number of arguments raises `ArgumentError`:

```ruby
# greet()          # ArgumentError (wrong number of arguments (given 0, expected 1))
# greet("a", "b")  # ArgumentError (given 2, expected 1)
```

- Methods defined at the top level are available everywhere in the file (they become private methods of `Object`). Methods inside classes are covered from Lesson 22.

Inspecting a method:

```ruby
m = method(:greet)
p m.arity           # 1
p m.parameters      # [[:req, :name]]
puts __method__.inspect   # inside a method, gives its own name as a symbol
```

---

## 12.2 Default and Optional Arguments

Give a parameter a default value with `=`:

```ruby
def greet(name, greeting = "Hello")
  "#{greeting}, #{name}!"
end

puts greet("Ada")             # Hello, Ada!
puts greet("Ada", "Welcome")  # Welcome, Ada!
```

- Defaults are evaluated **each time** the method is called, and may use earlier parameters or other methods:

```ruby
def log(message, time = Time.now)
  puts "[#{time}] #{message}"
end

def box(width, height = width)    # a square by default
  width * height
end
```

- Optional parameters are usually placed after required ones. Ruby can also handle optional parameters in the middle, but it is confusing, so avoid it:

```ruby
def example(a, b = 2, c)        # works, but not recommended
  [a, b, c]
end
p example(1, 3)    # [1, 2, 3]
```

- To tell whether the caller passed a value, use a unique default object or a keyword argument (below).

---

## 12.3 Keyword Arguments

Keyword arguments are passed **by name**, which makes calls self-explanatory and order-independent.

```ruby
def create_user(name:, age:, admin: false)
  "#{name} (#{age}) admin=#{admin}"
end

puts create_user(name: "Ada", age: 36)
puts create_user(age: 36, name: "Ada", admin: true)    # any order
```

| Syntax | Meaning |
|--------|---------|
| `name:` | **Required** keyword. A missing one raises `ArgumentError` |
| `name: "x"` | **Optional** keyword with a default |
| `**options` | Collects all other keywords into a hash |

```ruby
# create_user(name: "Ada")            # ArgumentError: missing keyword: :age
# create_user(name: "A", age: 1, x: 2) # ArgumentError: unknown keyword: :x
```

Passing a hash as keywords with `**`:

```ruby
attrs = { name: "Ada", age: 36 }
create_user(**attrs)
```

Mixing positional and keyword arguments:

```ruby
def send_email(to, subject, cc: nil, priority: :normal)
  "To #{to}: #{subject} cc=#{cc.inspect} priority=#{priority}"
end

puts send_email("a@x.com", "Hi", priority: :high)
```

Since Ruby 3, positional hashes and keyword arguments are kept **separate**: a plain hash is no longer silently converted into keywords, so use `**hash` when you mean keywords.

Shorthand when a local variable has the same name as the keyword (Ruby 3.1+):

```ruby
name = "Ada"
age = 36
create_user(name:, age:)       # same as name: name, age: age
```

---

## 12.4 Splat (*) and Double Splat (**)

### `*args`: collect extra positional arguments

```ruby
def sum(*numbers)
  numbers.sum
end

puts sum(1, 2, 3)      # 6
puts sum                # 0
puts sum(*[4, 5, 6])    # 15   (* also expands an array into separate arguments)
```

Mix with normal parameters. Ruby fills required ones first:

```ruby
def example(first, *middle, last)
  p [first, middle, last]
end
example(1, 2, 3, 4)    # [1, [2, 3], 4]
example(1, 2)          # [1, [], 2]
```

### `**opts`: collect extra keyword arguments

```ruby
def configure(name:, **options)
  p name
  p options
end

configure(name: "app", debug: true, port: 3000)
# "app"
# {debug: true, port: 3000}
```

### Parameter order

When combined, the order must be:

1. required positional
2. optional positional
3. `*splat`
4. required positional (after the splat)
5. required and optional keywords
6. `**double_splat`
7. `&block`

```ruby
def everything(a, b = 2, *rest, c, d:, e: 5, **others, &block)
end
```

### Anonymous and forwarded arguments

Pass everything on to another method without naming it:

```ruby
def wrapper(*, **, &)          # anonymous splats and block (Ruby 3.2+)
  target(*, **, &)
end

def forward_all(...)           # forward everything (Ruby 2.7+)
  target(...)
end
```

---

## 12.5 Implicit Return Values

A method returns the value of its **last evaluated expression**. The `return` keyword is optional.

```ruby
def add(a, b)
  a + b          # returned automatically
end

puts add(2, 3)   # 5
```

Use `return` for an **early exit**:

```ruby
def divide(a, b)
  return nil if b.zero?
  a / b
end
```

A method with no expression, or whose last expression is `puts`, returns `nil`:

```ruby
def shout(text)
  puts text.upcase
end
p shout("hi")     # prints HI, then p shows nil
```

### Returning several values

Return an array and destructure it:

```ruby
def min_max(list)
  return list.min, list.max       # same as [list.min, list.max]
end

low, high = min_max([3, 1, 4])
puts "#{low} #{high}"        # 1 4
```

### Conditionals as return values

```ruby
def category(age)
  if age < 13 then :child
  elsif age < 20 then :teen
  else :adult
  end
end
```

### `def` returns a symbol

```ruby
p(def hello; end)    # :hello
```

That allows tricks like `private def helper ... end` (Lesson 26).

---

## 12.6 Predicate (?) and Bang (!) Methods

These are **naming conventions**. The `?` and `!` are part of the method name and have no special power by themselves.

### Predicate methods: end with `?`

They answer a yes/no question and return `true` or `false` (or at least a truthy/falsy value):

```ruby
[].empty?          # true
5.even?            # true
"abc".include?("b")  # true
nil.nil?           # true

def adult?(age)
  age >= 18
end

puts "ok" if adult?(20)
```

### Bang methods: end with `!`

A bang signals "this is more dangerous than the plain version". Most often it means it **modifies the receiver in place**, or raises an error instead of returning `nil`.

```ruby
name = "ruby"
name.upcase       # "RUBY"   (returns a new string; name unchanged)
name.upcase!      # "RUBY"   (changes name itself)
name.upcase!      # nil      (nothing to change this time!)

list = [3, 1, 2]
list.sort         # new sorted array
list.sort!        # sorts list itself
list.map!(&:to_s) # transforms in place
```

Other bang examples: `save!` (raises on failure in Rails), `reverse!`, `gsub!`, `compact!`, `merge!`.

Warning: many bang methods return `nil` when nothing changed, so don't chain on their result or use it as a value.

### Writing your own

```ruby
class User
  def valid?
    !@name.nil?
  end

  def normalize!
    @name = @name.strip.downcase
    self
  end
end
```

Not every dangerous method has a `!`, and not every `!` method is the "bang" of a pair. Use them in your own code only when a safe counterpart also exists.

---

## 12.7 Endless Methods and Method Aliasing

### Endless methods (Ruby 3.0+)

A one-expression method can be written on one line with `=`:

```ruby
def square(x) = x * x
def greet(name) = "Hello, #{name}!"
def adult?(age) = age >= 18

puts square(5)       # 25
```

Use for short, simple bodies. For anything longer, use a normal `def ... end`.

### Aliasing

Give a method a second name:

```ruby
def calculate_total(a, b)
  a + b
end

alias total calculate_total          # alias new_name old_name (no commas, no symbols needed)
alias_method :sum_of, :calculate_total   # method form (inside classes), takes symbols or strings

puts total(1, 2)      # 3
```

Aliasing is mostly used inside classes, to provide friendlier names or to wrap an existing method (Lesson 29).

### Undefining

```ruby
undef total     # remove a method (inside a class, use undef_method)
```

---

## 12.8 Recursion

A **recursive** method calls itself. It needs a **base case** (when to stop) and a **recursive case** that moves toward it.

```ruby
def factorial(n)
  return 1 if n <= 1          # base case
  n * factorial(n - 1)        # recursive case
end

puts factorial(5)    # 120
```

How `factorial(3)` expands: `3 * factorial(2)` → `3 * (2 * factorial(1))` → `3 * (2 * 1)` → `6`.

Another example, summing a nested array:

```ruby
def deep_sum(items)
  items.sum do |item|
    item.is_a?(Array) ? deep_sum(item) : item
  end
end

puts deep_sum([1, [2, [3, 4]], 5])    # 15
```

### Naive Fibonacci vs memoization

The simple version recalculates the same values over and over, which becomes very slow:

```ruby
def fib(n)
  n < 2 ? n : fib(n - 1) + fib(n - 2)
end
```

**Memoization** stores results already computed:

```ruby
def fib_fast(n, memo = {})
  return n if n < 2
  memo[n] ||= fib_fast(n - 1, memo) + fib_fast(n - 2, memo)
end

puts fib_fast(80)      # 23416728348467685, computed instantly
```

Or with a hash that computes its own values:

```ruby
FIB = Hash.new { |hash, n| hash[n] = n < 2 ? n : hash[n - 1] + hash[n - 2] }
puts FIB[50]
```

### Limits of recursion

Every call uses stack space. Very deep recursion raises `SystemStackError: stack level too deep`:

```ruby
def forever(n) = forever(n + 1)
# forever(0)    # SystemStackError
```

Ruby does not guarantee tail-call optimization, so for very deep problems prefer loops or `each`/`reduce`:

```ruby
def factorial_loop(n) = (1..n).reduce(1, :*)
```

---

[Previous](./[11]-Loops-and-Iteration.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[13]-Blocks-Procs-and-Lambdas.md)