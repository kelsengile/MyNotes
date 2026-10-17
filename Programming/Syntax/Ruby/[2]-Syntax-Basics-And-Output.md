[Previous](./[1]-Getting-Started-With-Ruby.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[3]-Variables-And-Constants.md)

*Basics*

# Lesson 2 - Syntax Basics And Output

## 2.1 Comments

Comments are ignored by the interpreter.

```ruby
# This is a single-line comment
puts "Hello" # a comment can follow code

=begin
This is a block comment.
It must start at the very beginning of a line.
=end
```

Most Rubyists use repeated `#` lines instead of `=begin`/`=end`.

**Magic comments** appear on the first lines of a file and change how Ruby treats the file:

```ruby
# frozen_string_literal: true
```

`frozen_string_literal: true` makes every string literal in the file immutable, which saves memory and prevents accidental changes. It is common in modern Ruby projects. In Ruby 3.4 and later, code that mutates a string literal without the comment still works, but emits a warning when warnings are enabled; a future version is expected to freeze literals by default.

---

## 2.2 Printing Output

| Method | Behavior |
| --- | --- |
| `puts` | Prints the value and a newline; arrays print one element per line |
| `print` | Prints the value with no newline |
| `p` | Prints `inspect` output (good for debugging) and **returns** its argument |
| `pp` | Like `p`, but pretty-prints large nested structures |
| `putc` | Prints a single character |
| `printf` | C-style formatted output |

```ruby
puts "Hello"            # Hello
print "Hello"           # Hello (no newline)
puts [1, 2, 3]          # 1, 2, 3 on separate lines
p "Hello"               # "Hello"   (with quotes)
p nil                   # nil
puts nil                # (blank line)
pp({ name: "Ada", langs: ["Ruby", "C"] })
printf("%05.1f%%\n", 7.5)   # 007.5%
```

The difference between `puts` and `p` matters when debugging: `p "5"` and `p 5` look different, while `puts "5"` and `puts 5` look the same.

---

## 2.3 Reading Input

```ruby
print "What is your name? "
name = gets.chomp        # gets reads a line including the newline; chomp removes it
puts "Nice to meet you, #{name}!"

print "Age: "
age = gets.to_i          # convert the text to an Integer
puts "Next year you will be #{age + 1}."
```

Notes:

- `gets` returns `nil` at end of input, so `gets.chomp` raises `NoMethodError` if there is no input. Use `gets&.chomp` when that is possible.
- `STDIN.read` reads everything until end of input.
- Command-line arguments are in the array `ARGV`:

```ruby
# ruby greet.rb Alice Bob
puts "Hello, #{ARGV.join(' and ')}!"
```

- `$stdin`, `$stdout`, and `$stderr` are global stream objects; `warn "message"` writes to standard error.

---

## 2.4 Everything Is An Object

In Ruby, every value is an object that responds to methods.

```ruby
5.class            #=> Integer
3.14.class         #=> Float
"hi".class         #=> String
:sym.class         #=> Symbol
nil.class          #=> NilClass
true.class         #=> TrueClass
[1, 2].class       #=> Array
{ a: 1 }.class     #=> Hash
(1..5).class       #=> Range
String.class       #=> Class
```

Since numbers are objects, you can call methods on literals:

```ruby
-5.abs         #=> 5
10.digits      #=> [0, 1]
3.zero?        #=> false
"ruby".reverse #=> "ybur"
```

Ruby uses *message passing*: `obj.method(args)` sends the message `method` to `obj`. Most operators are also just methods: `1 + 2` is the same as `1.+(2)`.

---

## 2.5 Syntax Rules And Conventions

- **Line endings** end statements; semicolons are optional and rarely used. A line ending with an operator, comma, or dot continues on the next line. Since Ruby 4.0, a line may also *begin* with `&&`, `||`, `and`, or `or` to continue the previous expression.
- **Parentheses** around method arguments are optional: `puts("hi")` and `puts "hi"` are equivalent. Use parentheses when the call is ambiguous or nested.
- **Indentation** is not significant, but the community standard is **two spaces**.
- **Blocks of code** end with the keyword `end`.
- **Everything is an expression**; even `if` and `def` return values (`def` returns the method name as a symbol).

Naming conventions:

| Kind | Convention | Example |
| --- | --- | --- |
| Local variable, method | `snake_case` | `user_name`, `calculate_total` |
| Class, module | `PascalCase` | `ShoppingCart` |
| Constant | `SCREAMING_SNAKE_CASE` | `MAX_SIZE` |
| Instance variable | `@snake_case` | `@balance` |
| Class variable | `@@snake_case` | `@@count` |
| Global variable | `$snake_case` | `$debug` |
| Predicate method (returns true/false) | ends in `?` | `empty?` |
| Dangerous / mutating method | ends in `!` | `sort!` |
| Setter method | ends in `=` | `name=` |

---

## 2.6 Reserved Words

These keywords cannot be used as variable names:

```
alias    and      begin    BEGIN    break    case     class    def
defined? do       else     elsif    END      end      ensure   false
for      if       in       module   next     nil      not      or
redo     rescue   retry    return   self     super    then     true
undef    unless   until    when     while    yield
```

Also reserved are the pseudo-variables `__FILE__`, `__LINE__`, and `__ENCODING__`. `__FILE__` holds the current file name, and a common idiom runs code only when the file is executed directly:

```ruby
if __FILE__ == $PROGRAM_NAME
  puts "Running as a script"
end
```

---

## 2.7 Basic String Interpolation

Double-quoted strings evaluate `#{}`; single-quoted strings do not:

```ruby
language = "Ruby"
puts "I love #{language}"     #=> I love Ruby
puts 'I love #{language}'     #=> I love #{language}
puts "2 + 3 = #{2 + 3}"       #=> 2 + 3 = 5
```

Strings are covered in depth in Lesson 5.

---

[Previous](./[1]-Getting-Started-With-Ruby.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[3]-Variables-And-Constants.md)
