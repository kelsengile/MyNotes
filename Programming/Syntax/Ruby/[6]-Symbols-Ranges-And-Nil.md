[Previous](./[5]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[7]-Operators-And-Truthiness.md)

*Basics*

# Lesson 6 - Symbols Ranges And Nil

## 6.1 Symbols

A symbol is an immutable, unique name written with a leading colon. The same symbol is always the same object.

```ruby
:ruby
:"with spaces"
%s(another)
%i[red green blue]      #=> [:red, :green, :blue]

:a.equal?(:a)           #=> true
"a".equal?("a")         #=> false
:name.to_s              #=> "name"
"name".to_sym           #=> :name
:name.length            #=> 4
:a <=> :b               #=> -1
:hello.upcase           #=> :HELLO
```

Symbols are used as hash keys, option names, method names (`send(:upcase)`), and identifiers. Prefer them when you need a fixed label; prefer strings for text that changes or comes from users.

`Symbol#to_proc` provides a shortcut for blocks:

```ruby
%w[a b].map(&:upcase)   #=> ["A", "B"]
```

Symbols created from user input are garbage collected in modern Ruby, so the old "symbol memory leak" concern no longer applies.

---

## 6.2 Ranges

A `Range` represents a sequence of values with a start and an end.

```ruby
1..5          # inclusive: 1, 2, 3, 4, 5
1...5         # exclusive: 1, 2, 3, 4
"a".."e"      # works with any object that has succ and <=>
(1..)         # endless
(..5)         # beginless
```

```ruby
r = (1..10)
r.to_a            #=> [1, 2, ..., 10]
r.sum             #=> 55
r.include?(5)     #=> true
r.cover?(5.5)     #=> true
r === 3           #=> true (used by case/when)
r.min, r.max      #=> 1, 10
r.step(3).to_a    #=> [1, 4, 7, 10]
(1..10) % 3       # same as step(3)
r.each_slice(4).to_a
(1..Float::INFINITY).lazy.map { |x| x * 2 }.first(3)  #=> [2, 4, 6]
('a'..'e').to_a   #=> ["a", "b", "c", "d", "e"]
r.size            #=> 10
(1..10).minmax    #=> [1, 10]
```

Ranges also select parts of arrays and strings:

```ruby
[10, 20, 30, 40][1..2]   #=> [20, 30]
[10, 20, 30, 40][2..]    #=> [30, 40]
"hello"[..1]             #=> "he"
```

`include?` and `cover?` differ for non-numeric ranges: `cover?` only compares to the boundaries, while `include?` can iterate.

---

## 6.3 nil

`nil` represents "nothing". It is the only instance of `NilClass`.

```ruby
nil.class        #=> NilClass
nil.to_s         #=> ""
nil.to_a         #=> []
nil.to_i         #=> 0
nil.inspect      #=> "nil"
nil.nil?         #=> true
5.nil?           #=> false
```

Methods return `nil` when there is nothing to return: `[].first`, `{}[:missing]`, `puts`, an `if` whose condition fails without `else`, and so on.

Calling a method on `nil` is the origin of the classic `NoMethodError: undefined method 'x' for nil`. Protect against it:

```ruby
user = nil
user&.name               #=> nil   (safe navigation operator)
user.to_a                # use conversion
name = user&.name || "Anonymous"
(user && user.name)      # the older way
```

---

## 6.4 Booleans And Truthiness

`true` is the only instance of `TrueClass` and `false` of `FalseClass`.

In conditions, **only `false` and `nil` are falsy**. Everything else, including `0`, `""`, and `[]`, is truthy.

```ruby
puts "zero is truthy" if 0
puts "empty string is truthy" if ""
puts "empty array is truthy" if []
puts "nil is falsy" unless nil
```

Convert any value to a strict boolean with double negation:

```ruby
!!"hi"    #=> true
!!nil     #=> false
```

Logical operators return one of their operands rather than a pure boolean, which enables idioms like `name = input || "default"`.

---

## 6.5 Type Checking And Conversion

```ruby
5.is_a?(Integer)       #=> true
5.is_a?(Numeric)       #=> true
5.kind_of?(Comparable) #=> true
5.instance_of?(Numeric)#=> false (exact class only)
Integer === 5          #=> true
5.respond_to?(:+)      #=> true
5.class.ancestors      #=> [Integer, Numeric, Comparable, Object, Kernel, BasicObject]
```

Implicit vs explicit conversion:

| Explicit (you call) | Implicit (Ruby calls) | Strict conversion function |
| --- | --- | --- |
| `to_s` | `to_str` | `String(x)` |
| `to_i` | `to_int` | `Integer(x)` |
| `to_f` | | `Float(x)` |
| `to_a` | `to_ary` | `Array(x)` |
| `to_h` | `to_hash` | `Hash(x)` |
| `to_sym` | | |
| `to_r`, `to_c` | | `Rational(x)`, `Complex(x)` |

```ruby
Array(nil)       #=> []
Array([1, 2])    #=> [1, 2]
Array(1..3)      #=> [1, 2, 3]
Array("a")       #=> ["a"]
String(42)       #=> "42"
```

---

[Previous](./[5]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[7]-Operators-And-Truthiness.md)
