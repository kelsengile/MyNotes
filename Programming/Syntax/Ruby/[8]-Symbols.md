[Previous](./[7]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[9]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 8 - Symbols

A **symbol** is a lightweight, immutable name written with a leading colon, like `:status`. Symbols are used everywhere in Ruby, especially as hash keys and to refer to methods.

---

## 8.1 What Is a Symbol?

```ruby
:ruby
:first_name
:"with spaces"      # quotes allow unusual characters
:"id_#{5}"          # interpolation works: :id_5
:+                  # operators can be symbols too
:empty?             # so can methods ending in ? and !
```

Key properties:

- A symbol is an instance of `Symbol`.
- **Only one object exists per symbol name.** Every `:ruby` in your program is the *same* object.
- Symbols are **immutable**. You cannot change one.
- They are compared by identity, which is very fast.

```ruby
puts :ruby.class                 # Symbol
puts :ruby.object_id == :ruby.object_id   # true
puts :ruby.frozen?               # true
```

You can think of a symbol as "a name" or "a label", while a string is "some text".

### Symbol arrays

```ruby
%i[north south east west]    # [:north, :south, :east, :west]
%I[a#{1} b#{2}]              # with interpolation: [:a1, :b2]
```

---

## 8.2 Symbols vs Strings

Two equal strings are usually different objects, but equal symbols are the same object:

```ruby
puts "ruby".object_id == "ruby".object_id   # false (two separate Strings)
puts :ruby.object_id == :ruby.object_id     # true  (one Symbol)
puts "ruby".equal?("ruby")                  # false
puts :ruby.equal?(:ruby)                    # true
```

| | String | Symbol |
|---|---|---|
| Mutable | Yes (unless frozen) | No |
| Identity | New object each time | One object per name |
| Typical use | Text, data from users | Names, labels, keys, options |
| Has many text methods | Yes | Only a few |
| Written | `"name"` | `:name` |

Rules of thumb:

- Use a **string** when the content is *data*: text you display, read, edit, or receive from users or files.
- Use a **symbol** when the content is an *identifier* in your code: a hash key, an option name, a status, a method name.

```ruby
status = :active                 # a fixed label
user_name = "Ada Lovelace"       # text data
```

Symbols respond to some of the same methods as strings but return what makes sense:

```ruby
:hello.length        # 5
:hello.upcase        # :HELLO
:hello.to_s          # "hello"
:hello[0]            # "h"
:b <=> :a            # 1
:hello.start_with?("he")   # true
:hello.size          # 5
```

### Memory note

Symbols no longer live forever. Ruby can garbage-collect *dynamic* symbols (ones made at runtime with `to_sym`) that are no longer used. Even so, converting large amounts of **untrusted** input with `to_sym` is generally avoided, because it can still waste memory or complicate things.

---

## 8.3 Symbols as Hash Keys

The most common place you will see symbols is as keys in a hash:

```ruby
person = { name: "Ada", age: 36 }
puts person[:name]      # Ada
puts person[:age]       # 36
```

`{ name: "Ada" }` is shorthand for `{ :name => "Ada" }`. Both create a **symbol** key.

```ruby
{ :name => "Ada" } == { name: "Ada" }    # true
```

Keys written as strings are different keys:

```ruby
h = { "name" => "Ada", name: "Grace" }
puts h["name"]    # Ada
puts h[:name]     # Grace
```

A string-in-quotes before the colon still makes a **symbol**:

```ruby
{ "first name": "Ada" }       # { :"first name" => "Ada" }
```

Why symbols make good keys: lookups are fast, they never change, and they read cleanly. Symbol keys are also the standard for **keyword arguments** (Lesson 12):

```ruby
def greet(name:, greeting: "Hello")
  "#{greeting}, #{name}!"
end

puts greet(name: "Ada")
puts greet(name: "Ada", greeting: "Hi")
```

Other everyday uses of symbols:

```ruby
user.send(:name)                       # call a method by name (Lesson 35)
[1, 2, 3].map(&:to_s)                  # symbol-to-proc shortcut (Lesson 13)
[1, 2, 3].reduce(:+)                   # use a method name as the operation
attr_accessor :name, :age              # define attributes (Lesson 23)
respond_to?(:each)                     # ask whether a method exists
```

---

## 8.4 Converting Between Symbols and Strings

```ruby
:ruby.to_s          # "ruby"
:ruby.name          # "ruby" (a frozen, shared string; slightly more efficient)
:ruby.id2name       # "ruby" (an older alias)
"ruby".to_sym       # :ruby
"ruby".intern       # :ruby  (another name for to_sym)
"hello world".to_sym   # :"hello world"
```

Converting all keys of a hash:

```ruby
data = { "name" => "Ada", "age" => 36 }
puts data.transform_keys(&:to_sym)     # {name: "Ada", age: 36}

sym = { name: "Ada" }
puts sym.transform_keys(&:to_s)        # {"name" => "Ada"}
```

Converting each item in an array:

```ruby
%w[a b c].map(&:to_sym)       # [:a, :b, :c]
[:a, :b, :c].map(&:to_s)      # ["a", "b", "c"]
```

Remember: `:name` and `"name"` are **not equal**, and that mismatch is a classic source of bugs when reading JSON or form data (which gives string keys) and then looking things up with symbols:

```ruby
require "json"
parsed = JSON.parse('{"name": "Ada"}')
p parsed["name"]           # "Ada"
p parsed[:name]            # nil  (wrong key type!)

parsed = JSON.parse('{"name": "Ada"}', symbolize_names: true)
p parsed[:name]            # "Ada"
```

Listing symbols of the running program is possible with `Symbol.all_symbols`, but you will rarely need it.

---

[Previous](./[7]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[9]-Operators-and-Expressions.md)