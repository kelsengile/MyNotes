[Previous](./[4]-Ruby-Basics.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[6]-Numbers.md)

*Core Syntax*

# Lesson 5 - Variables & Constants (local, instance, class, global)

Variables give names to values so you can reuse them. Ruby has four kinds of variables, plus constants. The first character of the name tells Ruby which kind it is.

---

## 5.1 What Is a Variable?

A **variable** is a name that **refers to** an object. Assigning with `=` makes the name point at the object. It does not copy the object.

```ruby
name = "Ada"
age = 36
pi_approx = 3.14

puts name        # Ada
puts age + 1     # 37
```

Important ideas:

- **No declaration** is needed. Assigning creates the variable.
- **Assignment copies the reference, not the object.** Two names can point at the same object:

```ruby
a = "hello"
b = a            # b points at the same String
b << " world"    # modify that String in place
puts a           # hello world   (a changed too!)

c = a.dup        # a real copy
c << "!"
puts a           # hello world   (unchanged)
```

- Reassigning a variable points it at a **new** object and does not change the old one:

```ruby
x = 10
y = x
x = 20
puts y           # 10
```

Useful tools:

```ruby
x = 5
p defined?(x)         # "local-variable"
p defined?(zzz)       # nil
p x.object_id         # identity number of the object
p local_variables     # [:x, ...]
```

---

## 5.2 Dynamic Typing

Ruby is **dynamically typed**: variables have no fixed type. Only the **objects** have classes, and one variable can refer to different kinds of objects over time.

```ruby
thing = 42
puts thing.class     # Integer

thing = "forty-two"
puts thing.class     # String

thing = [4, 2]
puts thing.class     # Array
```

Ruby is also **strongly typed**: it will not silently convert between unrelated types.

```ruby
puts 1 + 2            # 3
# puts 1 + "2"        # TypeError: String can't be coerced into Integer
puts 1 + "2".to_i     # 3   (convert explicitly)
puts "1" + 2.to_s     # "12"
```

Because types are checked when code runs, mistakes appear at runtime. Good names, tests, and optional type tools (Lessons 73 to 75) help catch them.

---

## 5.3 Local, Instance, Class, and Global Variables

| Kind | Starts with | Visible | Example |
|------|-------------|---------|---------|
| **Local** | lowercase letter or `_` | Only within its scope (method, block, class body, or the file's top level) | `count = 0` |
| **Instance** | `@` | Inside one object, in all of its methods | `@name` |
| **Class** | `@@` | Shared by a class, its subclasses, and their instances | `@@total` |
| **Global** | `$` | Everywhere in the program | `$debug` |

### Local variables and scope

A local variable exists from the point it is assigned to the end of its scope. **`def`, `class`, and `module` start brand-new scopes** that cannot see outer local variables:

```ruby
greeting = "Hello"

def say
  puts greeting      # NameError: undefined local variable or method 'greeting'
end
```

Fix this by passing the value as an argument:

```ruby
def say(greeting)
  puts greeting
end
say("Hello")
```

**Blocks** (`do ... end` or `{ }`) are different. They **can see** outer variables, and variables first created inside a block disappear afterwards:

```ruby
total = 0
[1, 2, 3].each do |n|
  total += n          # can read and change the outer variable
  squared = n * n     # only exists inside the block
end
puts total            # 6
# puts squared        # NameError
```

`if`, `unless`, `while`, `case`, and `for` do **not** create a new scope:

```ruby
if true
  message = "created inside if"
end
puts message           # created inside if
```

### Instance variables

Instance variables belong to a single object. They are created when first assigned and are `nil` if you read one that was never set. You normally set them in `initialize` (Lesson 22).

```ruby
class Person
  def initialize(name)
    @name = name
  end

  def greet
    "Hi, I am #{@name}"
  end
end

puts Person.new("Ada").greet   # Hi, I am Ada
```

### Class variables

`@@variables` are shared across a class and everything that inherits from it. They are easy to misuse because subclasses share (and can change) them, so many Rubyists prefer *class instance variables* (Lesson 27).

```ruby
class Counter
  @@count = 0

  def initialize
    @@count += 1
  end

  def self.count
    @@count
  end
end

Counter.new
Counter.new
puts Counter.count    # 2
```

### Global variables

Globals are visible everywhere, which makes programs hard to reason about. Use them rarely. Ruby provides several useful built-in ones:

```ruby
$global_flag = true

puts $PROGRAM_NAME     # the script name (also $0)
puts $stdout.class     # IO
puts $stderr.class     # IO
puts $;.inspect        # (a special variable) the default split separator
puts __FILE__          # current file name (not a variable, a special keyword value)
```

Other built-ins you will meet: `$stdin`, `$!` (the last exception, Lesson 20), `$~` and `$1` (regex matches, Lesson 21), `ARGV`, and `ENV`.

### Naming recap

```ruby
local_var = 1
@instance_var = 2
@@class_var = 3        # only valid inside a class body or method
$global_var = 4
CONSTANT = 5
```

---

## 5.4 Constants and Reassignment Warnings

A **constant** is a name that starts with an **uppercase letter**. By convention, constants use `SCREAMING_SNAKE_CASE`, and they hold values that should not change.

```ruby
MAX_USERS = 100
GREETING = "Hello"
PI_ROUGH = 3.14

puts MAX_USERS
```

Ruby does **not** prevent reassignment. It only **warns**:

```ruby
MAX_USERS = 100
MAX_USERS = 200
# warning: already initialized constant MAX_USERS
# warning: previous definition of MAX_USERS was here
puts MAX_USERS   # 200
```

Also, a constant that points at a mutable object can still have that object changed:

```ruby
NAMES = ["Ada", "Alan"]
NAMES << "Grace"      # no warning! The array itself changed
p NAMES               # ["Ada", "Alan", "Grace"]
```

To truly protect it, **freeze** the object (Lesson 32):

```ruby
NAMES = ["Ada", "Alan"].freeze
# NAMES << "Grace"    # FrozenError: can't modify frozen Array
```

More about constants:

- They can live inside classes and modules and are accessed with `::`:

```ruby
module Config
  TIMEOUT = 30
end

puts Config::TIMEOUT    # 30
puts Math::PI           # 3.141592653589793
```

- **You cannot assign a constant inside a method body** (`SyntaxError: dynamic constant assignment`). Define constants at the class, module, or file level.
- Class names and module names are constants too (`String`, `Math`).
- You can look constants up by name with `Object.const_get(:MAX_USERS)` (Lesson 38).

---

## 5.5 Parallel Assignment and Swapping

Ruby can assign several variables in one statement. The right side is evaluated first, then the values are distributed.

```ruby
a, b, c = 1, 2, 3
puts a, b, c            # 1 2 3

x = y = 0               # chain: both are 0
```

### Swapping without a temporary variable

```ruby
a = 1
b = 2
a, b = b, a
puts "#{a} #{b}"        # 2 1
```

### Mismatched counts

```ruby
a, b = 1, 2, 3          # a=1, b=2 (3 is dropped)
a, b, c = 1, 2          # a=1, b=2, c=nil
```

### Splat collects the rest

```ruby
first, *rest = [1, 2, 3, 4]
p first     # 1
p rest      # [2, 3, 4]

*init, last = [1, 2, 3, 4]
p init      # [1, 2, 3]
p last      # 4

head, *middle, tail = 1, 2, 3, 4, 5
p middle    # [2, 3, 4]
```

### Destructuring arrays and nested arrays

```ruby
a, b = [10, 20]          # a=10, b=20

a, (b, c), d = 1, [2, 3], 4
p [a, b, c, d]           # [1, 2, 3, 4]
```

### Ignoring values

```ruby
_, second, _ = 1, 2, 3   # use _ for values you don't need
puts second              # 2
```

### Methods that return several values

```ruby
def min_max(numbers)
  [numbers.min, numbers.max]
end

low, high = min_max([5, 2, 9])
puts "#{low}..#{high}"   # 2..9
```

### Operator assignment

```ruby
count = 0
count += 1       # count = count + 1
count -= 1
count *= 5
count **= 2
name = nil
name ||= "Guest" # assign only if name is nil or false
```

(Operator assignment is covered fully in Lesson 9.)

---

[Previous](./[4]-Ruby-Basics.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[6]-Numbers.md)