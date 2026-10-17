[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[5]-Variables-and-Constants.md)

*Getting Started*

# Lesson 4 - Ruby Basics: Syntax, Comments, Output & Input

This lesson covers the building blocks you will use in every program: writing code, leaving notes, printing results, and reading what the user types.

---

## 4.1 Your First Program

```ruby
puts "Hello, World!"
```

Save it as `hello.rb` and run `ruby hello.rb`. Things to notice:

- `puts` is a **method** that prints its argument followed by a newline.
- There are **no semicolons** and **no parentheses required**. `puts("Hello, World!")` means the same thing.
- There is **no `main` function** and no class required.

A slightly larger program shows more of the style:

```ruby
name = "Ada"
age = 36

puts "Name: #{name}"
puts "Next year #{name} will be #{age + 1}."

if age >= 18
  puts "Adult"
else
  puts "Minor"
end
```

Basic syntax rules:

| Rule | Example |
|------|---------|
| One statement per line; newline ends it | `x = 5` |
| Semicolons let you put several on a line (rarely used) | `a = 1; b = 2` |
| Blocks of code end with `end` | `if ... end`, `def ... end` |
| Indentation is **not required** but convention is 2 spaces | |
| Parentheses on method calls are optional | `puts "hi"` or `puts("hi")` |
| A line continues if it ends with an operator, comma, or `\`, or the next line begins with `.method` | `total = 1 +` newline `2` |

```ruby
result = [1, 2, 3, 4]
  .map { |n| n * 2 }
  .select { |n| n > 4 }
```

---

## 4.2 Everything Is an Object

In Ruby **every value is an object**, including numbers, `true`, and `nil`. Every object has a class and responds to methods:

```ruby
puts 5.class              # Integer
puts 3.14.class           # Float
puts "hi".class           # String
puts :sym.class           # Symbol
puts [1, 2].class         # Array
puts({ a: 1 }.class)      # Hash
puts nil.class            # NilClass
puts true.class           # TrueClass
puts (1..3).class         # Range
puts String.class         # Class  (even classes are objects)
```

You can call methods directly on literals:

```ruby
puts -7.abs               # 7
puts 5.between?(1, 10)    # true
puts "ruby".upcase        # RUBY
puts 3.times.to_a.inspect # [0, 1, 2]
```

Ways to explore an object:

```ruby
5.is_a?(Integer)               # true
5.respond_to?(:even?)          # true
5.methods.first(5)             # a list of method names
"abc".instance_of?(String)     # true
5.object_id                    # a number identifying the object
```

There are no "primitive types" with special syntax such as Java's `int`. This consistency is a big reason Ruby feels so uniform.

---

## 4.3 Comments and Documentation Comments

### Single-line comments

Everything after `#` on a line is ignored by Ruby.

```ruby
# This whole line is a comment
x = 10 # and this is a trailing comment
```

### Block comments

```ruby
=begin
This is a multi-line comment.
=begin and =end must start at the very beginning of a line.
=end
```

Most Rubyists simply put `#` on every line instead.

### Magic comments

Special comments at the **top of a file** change how Ruby treats it. The most common:

```ruby
# frozen_string_literal: true
```

This makes every string literal in the file **frozen** (immutable), which saves memory and prevents accidental changes (see Lesson 7).

### Documentation comments

Comments placed directly above a class or method are used by documentation tools (**RDoc** and **YARD**). They are written in simple markup:

```ruby
# Calculates the area of a circle.
#
# @param radius [Numeric] the radius of the circle
# @return [Float] the area
#
# @example
#   area(2) # => 12.566370614359172
def area(radius)
  Math::PI * radius**2
end
```

Good commenting advice: explain **why**, not **what**. The code already says what it does.

### `__END__` and `DATA`

Everything after a line containing only `__END__` is not Ruby code, but you can read it with the `DATA` object:

```ruby
DATA.each_line { |line| puts line.upcase }

__END__
first line
second line
```

---

## 4.4 puts, print, p, and pp

Ruby has four common printing methods. They differ in *how* they format their output:

| Method | Adds newline? | Converts with | Returns | Typical use |
|--------|---------------|---------------|---------|-------------|
| `puts` | Yes | `to_s` | `nil` | Output for people |
| `print` | No | `to_s` | `nil` | Prompts, building a line piece by piece |
| `p` | Yes | `inspect` | The argument | **Debugging** |
| `pp` | Yes | `inspect`, pretty-printed | The argument | Debugging large nested data |

```ruby
puts "Hello"        # Hello
print "Hello"       # Hello   (no newline, the next output continues the line)
print "\n"
p "Hello"           # "Hello"   (quotes show it is a String)
p :sym              # :sym
p nil               # nil
puts nil            # (prints an empty line)
puts [1, [2, 3]]    # 1, 2, 3 each on its own line (arrays are flattened)
p [1, [2, 3]]       # [1, [2, 3]]
puts 1, 2, 3        # three lines
```

`p` shows the **programmer-friendly** form. This is why it is the debugging method of choice:

```ruby
puts "5"     # 5
puts 5       # 5      (you can't tell them apart!)
p "5"        # "5"
p 5          # 5
```

`pp` formats big structures over several lines:

```ruby
data = { user: { name: "Ada", langs: %w[Ruby Python], address: { city: "London", zip: "N1" } } }
pp data
```

Other useful output tools:

```ruby
$stdout.puts "to standard output"   # same as puts
$stderr.puts "to standard error"    # error messages
warn "a warning"                    # writes to stderr
puts "Tab:\tNewline:\\n"            # escape sequences work in double-quoted strings
puts "Total: %.2f" % 3.14159        # formatted output (Lesson 7)
```

---

## 4.5 Reading Input with gets

`gets` reads one line from standard input (usually the keyboard) and returns it as a **string including the trailing newline**. It returns `nil` at end of input.

```ruby
print "What is your name? "
name = gets.chomp         # chomp removes the trailing newline
puts "Hello, #{name}!"
```

Input is always a **String**. Convert it when you need a number:

```ruby
print "Enter your age: "
age = gets.chomp.to_i      # "36\n" -> "36" -> 36
puts "Next year you will be #{age + 1}."
```

| Method | Behavior |
|--------|----------|
| `gets` | Reads a line (from files named in `ARGV` if any were given, otherwise standard input) |
| `$stdin.gets` / `STDIN.gets` | Always reads from standard input |
| `.chomp` | Removes the trailing newline |
| `.to_i`, `.to_f` | Convert to Integer / Float (invalid text becomes `0`) |
| `Integer("42")` | Strict conversion; raises `ArgumentError` on bad input |
| `$stdin.read` | Reads **all** input until the end |
| `$stdin.each_line` | Loops over every line |

Handling the possibility of no input (e.g. when input is piped in and runs out):

```ruby
line = gets
if line.nil?
  puts "No input"
else
  puts "You typed: #{line.chomp}"
end

# or process all lines:
while (line = gets)
  puts line.chomp.reverse
end
```

For command-line arguments rather than interactive input, use `ARGV` (Lesson 2 and Lesson 54).

---

## 4.6 Naming Conventions

Ruby enforces some of these with syntax and the community follows the rest. RuboCop checks them for you.

| Kind | Convention | Examples |
|------|------------|----------|
| Local variables and method names | `snake_case` | `user_name`, `calculate_total` |
| Methods that return true/false | end in `?` | `empty?`, `valid?` |
| "Dangerous" or mutating methods | end in `!` | `sort!`, `save!` |
| Classes and modules | `PascalCase` (CamelCase) | `ShoppingCart`, `HttpClient` |
| Constants | `SCREAMING_SNAKE_CASE` | `MAX_SIZE`, `PI` |
| Instance variables | `@snake_case` | `@name` |
| Class variables | `@@snake_case` | `@@count` |
| Global variables | `$snake_case` | `$debug` |
| Symbols | `:snake_case` | `:first_name` |
| Files | `snake_case.rb` | `shopping_cart.rb` |
| Unused variables | start with `_` | `_unused`, `_` |

Rules enforced by the language:

- Local variables and methods **must start with a lowercase letter or underscore**.
- Anything starting with an **uppercase letter** is treated as a **constant**.
- Names can contain letters, digits, and underscores, but cannot start with a digit.
- Reserved words such as `class`, `end`, `if`, `def`, `nil`, `self` cannot be used as variable names.

```ruby
user_name = "Ada"        # good
MAX_RETRIES = 3          # constant
class OrderItem; end     # class
# 2fast = 1              # SyntaxError: cannot start with a digit
```

---

[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[5]-Variables-and-Constants.md)