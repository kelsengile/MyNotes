[Previous](./[17]-Enumerable-and-Enumerators.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[19]-Pattern-Matching.md)

*Core Syntax*

# Lesson 18 - Truthiness, nil & Equality

This lesson explains what Ruby considers true or false, what `nil` really is, the four different ways to ask "are these equal?", and how to write code that does not crash on missing values.

---

## 18.1 What Counts as True and False

Ruby has exactly **two falsy values**:

- `false`
- `nil`

**Everything else is truthy**, including values that are falsy in other languages:

```ruby
def check(value)
  value ? "truthy" : "falsy"
end

puts check(true)       # truthy
puts check(0)          # truthy   (not falsy, unlike C, JavaScript, Python)
puts check("")         # truthy   (an empty string)
puts check([])         # truthy   (an empty array)
puts check({})         # truthy
puts check("false")    # truthy   (a non-empty string)
puts check(false)      # falsy
puts check(nil)        # falsy
```

This is used by `if`, `unless`, `while`, `&&`, `||`, `!`, ternary, and so on.

### Converting to a strict boolean

`!!value` (double negation) turns any value into `true` or `false`:

```ruby
!!nil        # false
!!0          # true
!!"text"     # true
!"text"      # false
```

### Checking emptiness explicitly

Because `""` and `[]` are truthy, test for emptiness yourself:

```ruby
name = ""
puts "no name" if name.empty?
puts "no items" if list.empty?
puts "blank" if name.strip.empty?
puts "zero" if count.zero?
```

(Rails adds `blank?` and `present?`, but they are not part of plain Ruby.)

### The classes `true` and `false`

```ruby
true.class      # TrueClass
false.class     # FalseClass
true & false    # false  (non-short-circuit logic, also |, ^)
true.to_s       # "true"
```

---

## 18.2 nil and NilClass

`nil` is Ruby's "nothing" or "no value" object. It is the only instance of `NilClass`, and, being an object, it responds to methods.

```ruby
nil.class        # NilClass
nil.nil?         # true
nil.to_s         # ""
nil.to_a         # []
nil.to_i         # 0
nil.to_h         # {}
nil.inspect      # "nil"
nil & true       # false
nil | 5          # true
```

Things that return `nil`:

```ruby
[1, 2][10]                  # nil   (index out of range)
{ a: 1 }[:b]                # nil   (missing key)
puts "hi"                   # returns nil (after printing)
"abc".index("z")            # nil   (not found)
@never_set                  # nil   (an unset instance variable)
def nothing; end; nothing   # nil   (an empty method)
if false then 1 end         # nil   (no branch ran)
x = nil
```

### The famous error

Calling a method that `nil` doesn't have gives the most common Ruby error:

```ruby
user = nil
user.name
# NoMethodError: undefined method 'name' for nil
```

Read it as: "something I expected to be an object was `nil`." The task is to find *where the nil came from*.

### Checking for nil

```ruby
value.nil?           # true only for nil (false.nil? is false)
value == nil         # works but less idiomatic
!value               # true for nil AND false
```

### nil is a single object

```ruby
nil.object_id     # 8 (the same object every time)
nil.equal?(nil)   # true
```

---

## 18.3 ==, eql?, equal?, and ===

Ruby has four equality tests. They answer different questions.

| Method | Question it answers | Typical use |
|--------|--------------------|-------------|
| `==` | Do these have the **same value**? | Everyday comparison |
| `eql?` | Same value **and same type**? | Hash key comparison |
| `equal?` | Are they the **very same object**? | Identity checks |
| `===` | Does this value **belong to** that pattern/category? | `case/when` |

### `==` : value equality

```ruby
1 == 1          # true
1 == 1.0        # true   (numbers are converted to compare)
"a" == "a"      # true   (two different String objects, same contents)
[1, 2] == [1, 2]    # true
{ a: 1 } == { a: 1 } # true
:a == :a        # true
"1" == 1        # false
1 != 2          # true   (!= is the opposite of ==)
```

Classes can define their own `==` (Lesson 22 and Lesson 30).

### `eql?` : value **and** type

```ruby
1.eql?(1)         # true
1.eql?(1.0)       # false   (Integer vs Float)
"a".eql?("a")     # true
[1].eql?([1.0])   # false
```

Hash keys use `eql?` (together with `hash`), which is why `1` and `1.0` are different keys:

```ruby
h = { 1 => "int" }
h[1]       # "int"
h[1.0]     # nil
```

### `equal?` : identity

```ruby
a = "hello"
b = "hello"
c = a

a == b         # true   (same value)
a.equal?(b)    # false  (two different objects)
a.equal?(c)    # true   (c points at a's object)
:x.equal?(:x)  # true   (symbols are unique)
1.equal?(1)    # true   (small integers are the same object)
nil.equal?(nil) # true
```

Don't override `equal?`.

### `===` : case equality

`===` is used by `case/when`, `grep`, and some `Enumerable` methods. Its meaning depends on the class of the **left** side:

```ruby
Integer === 3          # true     (is 3 an Integer?)
String === "x"         # true
(1..10) === 5          # true     (is 5 within the range?)
/ab/ === "cabin"       # true     (does the regex match?)
:a === :a              # true
->(n) { n > 3 } === 5  # true     (calls the lambda)
Set[1, 2] === 1        # true
3 === Integer          # false    (order matters!)
```

Usage:

```ruby
case value
when Integer, Float then "number"      # calls Integer === value, then Float === value
when /^\d+$/       then "digits"
when 1..5          then "small"
end

%w[apple 12 pear 7].grep(/\d/)         # ["12", "7"]
[1, "a", 2.5, :b].grep(Numeric)        # [1, 2.5]
[1, 5, 10].grep(2..8)                  # [5]
```

### Comparable and <=>

If a class defines `<=>` and includes `Comparable`, it gets `==`, `<`, `>`, `between?`, and `clamp` (Lesson 30).

---

## 18.4 Object Identity

Every object has a unique **`object_id`** while it exists. Two variables with the same `object_id` point at the same object.

```ruby
a = "text"
b = a
c = "text"

puts a.object_id == b.object_id    # true
puts a.object_id == c.object_id    # false
puts a.equal?(c)                   # false
```

Special objects that are always the same instance:

```ruby
nil.object_id == nil.object_id         # true
true.object_id == true.object_id       # true
:sym.object_id == :sym.object_id       # true
1.object_id == 1.object_id             # true  (small integers)
"s".frozen?                            # depends on the magic comment
```

### Why identity matters: mutation

Because variables hold references, changing an object through one name is visible through all of them (Lesson 5):

```ruby
original = [1, 2, 3]
alias_ref = original
alias_ref << 4
p original              # [1, 2, 3, 4]

copy = original.dup
copy << 5
p original              # [1, 2, 3, 4]  (unchanged)
```

Method arguments are passed **by object reference**: the method gets a reference to the same object, so mutating it affects the caller:

```ruby
def add_item(list)
  list << "new"        # mutates the caller's array
end

def replace_list(list)
  list = ["other"]     # only rebinds the local variable; the caller is unaffected
end

items = []
add_item(items)
p items                # ["new"]
replace_list(items)
p items                # ["new"]
```

### Frozen literals share identity

```ruby
# frozen_string_literal: true
"a".equal?("a")        # true   (same frozen object when written identically in one file)
```

Without the magic comment each literal is a new object.

---

## 18.5 Avoiding nil Errors

`nil` is a common source of bugs, so Ruby gives you many tools.

### 1. Safe navigation `&.`

```ruby
user&.profile&.email           # nil if any link is nil
user&.name.to_s                # careful: to_s runs on nil's result as well
```

### 2. Defaults with `||` and `||=`

```ruby
name = params[:name] || "Guest"
@cache ||= {}
```

Careful: `||` replaces `false` too. When `false` is a legitimate value, use `nil?`:

```ruby
enabled = options.fetch(:enabled, true)     # only uses true when the key is missing
```

### 3. `fetch` and `dig`

```ruby
config.fetch(:port, 3000)          # default if the key is missing
config.fetch(:port)                # raises KeyError instead of silently returning nil
data.dig(:user, :address, :city)   # nil-safe deep access
```

### 4. Conversion methods that turn nil into something harmless

```ruby
nil.to_s     # ""
nil.to_a     # []
nil.to_i     # 0
Array(nil)   # []
Array(x)     # wraps a single value, leaves arrays alone, makes nil an empty array
String(nil)  # ""
"#{nil}"     # ""
```

```ruby
text = input.to_s.strip      # works even if input is nil
list = Array(maybe_list)     # always an array
```

### 5. Guard clauses

```ruby
def send_welcome(user)
  return unless user
  return if user.email.nil?
  Mailer.deliver(user.email)
end
```

### 6. Removing nils from collections

```ruby
[1, nil, 2].compact                # [1, 2]
{ a: 1, b: nil }.compact           # {a: 1}
list.map { |x| x&.upcase }.compact
list.filter_map { |x| x&.upcase }
```

### 7. Raise early with a clear message

Sometimes `nil` means a real error that should be reported at its source:

```ruby
def process(order)
  raise ArgumentError, "order is required" if order.nil?
  # ...
end

user = find_user(id) or raise "User #{id} not found"
```

### 8. The Null Object pattern

Return an object that safely does nothing instead of `nil`:

```ruby
class GuestUser
  def name = "Guest"
  def admin? = false
end

user = find_user(id) || GuestUser.new
puts user.name          # no nil checks needed
```

### 9. Prefer methods that don't return nil

```ruby
list.first.to_s                    # hmm, still nil-prone
list.first(1)                      # [] instead of nil when empty
list.sum                           # 0 for an empty list (not nil)
"abc".to_s
```

### 10. Reading the error

When you do get `NoMethodError: undefined method 'x' for nil`, check:

1. Which variable is nil? (Look at the line in the error message.)
2. Where was it set? Did a `find`, `[]`, `match`, `gets`, or `first` return nil?
3. Should nil be possible there? If yes, handle it. If no, fix the code that produced it.

---

[Previous](./[17]-Enumerable-and-Enumerators.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[19]-Pattern-Matching.md)