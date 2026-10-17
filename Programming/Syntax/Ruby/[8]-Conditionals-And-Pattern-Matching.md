[Previous](./[7]-Operators-And-Truthiness.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[9]-Loops-And-Iteration.md)

*Control Flow*

# Lesson 8 - Conditionals And Pattern Matching

## 8.1 if, elsif, else

```ruby
score = 85

if score >= 90
  puts "A"
elsif score >= 80
  puts "B"
else
  puts "C or below"
end
```

Conditions do not need parentheses, and `then` can be used for one-line forms: `if x > 5 then puts "big" end`.

`if` is an **expression**: it returns the value of the branch that ran.

```ruby
grade = if score >= 90 then "A" elsif score >= 80 then "B" else "C" end
```

---

## 8.2 unless, Modifiers, And The Ternary

`unless` is the opposite of `if`. Avoid `unless ... else` since it is confusing.

```ruby
unless logged_in
  puts "Please log in"
end
```

Statement modifiers put the condition at the end for short statements:

```ruby
puts "Adult" if age >= 18
puts "Minor" unless age >= 18
```

The ternary operator picks between two values:

```ruby
status = age >= 18 ? "adult" : "minor"
```

Guard clauses make code flat and readable:

```ruby
def process(order)
  return if order.nil?
  return unless order.paid?
  # main logic
end
```

---

## 8.3 case / when

`case` compares a value against each `when` using `===`, so it works with many kinds of conditions:

```ruby
def describe(x)
  case x
  when 0              then "zero"
  when 1..9           then "single digit"
  when Integer        then "big integer"
  when "hi", "hello"  then "greeting"
  when /^\d+$/        then "numeric string"
  when ->(n) { n.respond_to?(:each) } then "collection"
  when nil            then "nothing"
  else "unknown"
  end
end
```

`case` also returns a value:

```ruby
size = case weight
       when 0...10  then :small
       when 10...50 then :medium
       else :large
       end
```

`case` without a subject acts like an `if/elsif` chain:

```ruby
case
when temp < 0  then "freezing"
when temp < 20 then "cool"
else "warm"
end
```

Ruby does not fall through between `when` branches, and no `break` is needed.

---

## 8.4 Pattern Matching With case / in

Pattern matching (stable since Ruby 3.0, with later improvements) matches both **shape** and **value** and extracts parts into variables.

```ruby
config = { db: { user: "admin", port: 5432 }, mode: "dev" }

case config
in { db: { user:, port: Integer => port }, mode: "dev" }
  puts "dev db for #{user} on #{port}"
in { mode: "prod" }
  puts "production"
end
```

Pattern kinds:

```ruby
case [1, [2, 3]]
in [Integer => a, [b, *rest]]     # array pattern with nesting
  p [a, b, rest]                  #=> [1, 2, [3]]
end

case { name: "Ada", role: :admin }
in { name: String => name, role: :admin | :owner }   # alternatives
  puts "#{name} has power"
end

case 7
in Integer => n if n.odd?          # guard clause
  puts "odd #{n}"
end

expected = 5
case 5
in ^expected                        # pin operator: match existing value
  puts "matches"
end

case [1, "x", :y, 42, "z"]
in [*, Integer => found, *post]    # find pattern
  puts found                       #=> 1
end

case { status: "ok", extra: 1 }
in { status: "ok", **rest }        # capture the remaining keys
  p rest                           #=> {extra: 1}
end
```

One-line forms:

```ruby
result = { id: 1, name: "Ada" }
result => { name: }      # raises NoMatchingPatternKeyError if it doesn't fit
puts name                #=> Ada
result in { id: Integer } #=> true / false (boolean)
```

If no `in` clause matches and there is no `else`, a `NoMatchingPatternError` is raised.

Your own classes can take part by defining `deconstruct` (for array patterns) and `deconstruct_keys` (for hash patterns). `Struct` and `Data` do this automatically.

```ruby
Point = Data.define(:x, :y)
case Point.new(x: 1, y: 2)
in { x:, y: } then puts "x=#{x}, y=#{y}"
end
```

---

## 8.5 Truthiness In Practice

Only `nil` and `false` are falsy, so these idioms work:

```ruby
name = params[:name] || "guest"
puts "has items" unless items.empty?
puts "found" if list.find { |x| x > 3 }
```

Be careful when `false` is a valid value: `value ||= default` overrides a legitimate `false`. In that case check explicitly: `value = default if value.nil?`.

---

[Previous](./[7]-Operators-And-Truthiness.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[9]-Loops-And-Iteration.md)
