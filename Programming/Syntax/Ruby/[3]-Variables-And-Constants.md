[Previous](./[2]-Syntax-Basics-And-Output.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[4]-Numbers-And-Math.md)

*Basics*

# Lesson 3 - Variables And Constants

## 3.1 Creating Variables

A variable is a name that refers to an object. In Ruby you create one simply by assigning to it; no declaration or type is needed.

```ruby
age = 30
name = "Ada"
pi = 3.14159
active = true
```

Rules for local variable names: start with a lowercase letter or underscore, then letters, digits, or underscores (`user_name`, `_temp`, `item2`). Use `snake_case`.

Assignment is an expression and returns the assigned value, so it can be chained:

```ruby
a = b = c = 0
puts a + b + c   #=> 0
```

---

## 3.2 Dynamic Typing

Variables have no type. The *value* has a type, and a variable can be reassigned to a value of any type:

```ruby
x = 10
x = "ten"
x = [1, 0]
puts x.class   #=> Array
```

Ruby is also *strongly* typed: it will not silently mix incompatible types.

```ruby
"5" + 5        # TypeError: no implicit conversion of Integer into String
"5" + 5.to_s   #=> "55"
"5".to_i + 5   #=> 10
```

Duck typing: Ruby cares about what an object **can do** (which methods it responds to), not what class it is.

---

## 3.3 Multiple Assignment

```ruby
a, b, c = 1, 2, 3
a, b = b, a            # swap without a temporary variable

first, *rest = [1, 2, 3, 4]
first   #=> 1
rest    #=> [2, 3, 4]

*init, last = [1, 2, 3, 4]
init    #=> [1, 2, 3]
last    #=> 4

head, (x, y), tail = 1, [2, 3], 4   # nested destructuring
x       #=> 2

q, r = 17.divmod(5)    # methods can return several values as an array
q       #=> 3
r       #=> 2
```

If there are fewer values than variables, the extras are `nil`.

---

## 3.4 Variable Scope And Kinds

| Kind | Prefix | Visibility |
| --- | --- | --- |
| Local | none | The current method, block, class body, or file |
| Instance | `@` | One object; accessible from its instance methods |
| Class variable | `@@` | Shared across a class and its subclasses |
| Global | `$` | Entire program |
| Constant | Capital letter | The lexical scope where it is defined (and accessible through `::`) |

```ruby
x = 10

def show
  puts x   # NameError: undefined local variable or method 'x'
end
```

Methods (`def`), classes, and modules start a **new scope** and cannot see outer local variables. Blocks, however, *can* see them (they are closures):

```ruby
total = 0
[1, 2, 3].each { |n| total += n }
puts total   #=> 6
```

Variables created inside a block disappear when the block ends. Block parameters shadow outer variables of the same name; to declare a variable that is only local to the block, list it after a semicolon: `|n; temp|`.

Special global variables include `$stdout`, `$stderr`, `$0` (script name), `$PROGRAM_NAME`, and `$~` (last regex match). Prefer passing arguments over using globals.

---

## 3.5 Constants

Names that begin with an uppercase letter are constants. By convention they use `SCREAMING_SNAKE_CASE`.

```ruby
MAX_USERS = 100
GREETING = "Hello"
puts MAX_USERS
```

Ruby does **not** prevent re-assignment; it only prints a warning:

```ruby
MAX_USERS = 200   # warning: already initialized constant MAX_USERS
```

Constants cannot be defined inside a method body. Inside classes and modules they are accessed with `::`:

```ruby
module Config
  TIMEOUT = 30
end
puts Config::TIMEOUT   #=> 30
```

Useful related methods: `Object.const_get(:MAX_USERS)`, `Module#constants`, and `private_constant` (covered in Lesson 15).

---

## 3.6 Mutability, Freezing, And Object Identity

Variables hold **references** to objects. Assigning one variable to another copies the reference, not the object:

```ruby
a = "hello"
b = a
b << " world"       # mutates the shared string
puts a              #=> hello world
puts a.equal?(b)    #=> true (same object)
puts a.object_id == b.object_id   #=> true
```

Make a copy when you need independence:

```ruby
c = a.dup           # shallow copy
c << "!"
puts a              #=> hello world (unchanged)
```

`dup` and `clone` make *shallow* copies: nested objects are still shared.

**Freezing** makes an object read-only:

```ruby
LIST = %w[a b c].freeze
LIST << "d"              # FrozenError: can't modify frozen Array
LIST.frozen?             #=> true
```

`freeze` is shallow: the strings inside `LIST` can still be changed unless they are frozen too. Integers, symbols, `nil`, `true`, and `false` are always immutable.

---

## 3.7 Handy Assignment Tools

```ruby
count = 0
count += 1          # same as count = count + 1
count -= 1
count *= 3
count **= 2

name = nil
name ||= "guest"    # assign only if name is nil or false
name &&= name.upcase # assign only if name is truthy

defined?(count)     #=> "local-variable"
defined?(puts)      #=> "method"
defined?(String)    #=> "constant"
defined?(zzz)       #=> nil
```

`defined?` returns a description string, or `nil` when the name is not defined.

---

[Previous](./[2]-Syntax-Basics-And-Output.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[4]-Numbers-And-Math.md)
