[Previous](./[9]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[11]-Loops-and-Iteration.md)

*Core Syntax*

# [10]-Conditionals

# Lesson 10 - Conditionals (if, unless, case/when, ternary)

Conditionals let your program make decisions. A condition is any expression: Ruby treats `false` and `nil` as false and **everything else as true** (Lesson 18).

---

## 10.1 if, elsif, and else

```ruby
temperature = 25

if temperature > 30
  puts "Hot"
elsif temperature > 20
  puts "Warm"
elsif temperature > 10
  puts "Cool"
else
  puts "Cold"
end
```

Rules and tips:

- The keyword is **`elsif`** (not `elseif` or `else if`).
- `end` closes the whole structure.
- Parentheses around the condition are not needed.
- `then` lets you write a branch on one line:

```ruby
if age >= 18 then puts "adult" end
```

- Combine conditions with `&&`, `||`, and `!`:

```ruby
if age >= 18 && has_ticket
  puts "Welcome"
end

if role == "admin" || role == "owner"
  puts "Full access"
end
```

- Test several values with `include?`:

```ruby
if %w[admin owner].include?(role)
  puts "Full access"
end
```

- Assigning inside a condition is allowed but needs care (`=` assigns, `==` compares). Wrap it in parentheses to show it is on purpose:

```ruby
if (match = text.match(/\d+/))
  puts match[0]
end
```

A common beginner error is `if x = 5` (assignment, always truthy) instead of `if x == 5`. Ruby warns about this with `-w`.

### Guard clauses

Return early to avoid deep nesting:

```ruby
def process(order)
  return "no order" if order.nil?
  return "empty" if order.items.empty?

  "processing #{order.items.size} items"
end
```

---

## 10.2 unless

`unless` is the opposite of `if`: it runs the code when the condition is **false**.

```ruby
unless logged_in
  puts "Please log in"
end

# same as:
if !logged_in
  puts "Please log in"
end
```

```ruby
unless age >= 18
  puts "Minor"
else
  puts "Adult"
end
```

Guidelines:

- Use `unless` for simple, single-condition negations. It reads naturally: *"unless the file exists, create it"*.
- **Avoid `unless` with `else`** (use `if` instead) and **avoid combining it with `&&`/`||`**, because the double negatives are hard to read.
- There is **no `elsif` for `unless`**.

---

## 10.3 case / when

`case` compares one value against several options. It is cleaner than a long `if/elsif` chain.

```ruby
day = "sat"

case day
when "mon", "tue", "wed", "thu", "fri"
  puts "Weekday"
when "sat", "sun"
  puts "Weekend"
else
  puts "Unknown"
end
```

- Several values can share a branch, separated by commas.
- Matching uses the **`===` operator** (case equality) on each `when` value: `when_value === case_value`. That means `when` accepts much more than plain values.

### What `when` can match

```ruby
case score
when 90..100   then "A"               # ranges
when 80...90   then "B"
when 0...80    then "C or below"
end

case value
when Integer   then "an integer"      # classes (is_a?)
when String    then "a string"
when nil       then "nil"
when /^\d+$/   then "digits"          # regular expressions (for strings)
when ->(x) { x.respond_to?(:each) } then "iterable"   # lambdas (called with the value)
end
```

```ruby
case "hello world"
when /world/   then puts "found world"
end

case 15
when 1..10   then puts "low"
when 11..20  then puts "medium"
end
```

Spreading an array with `*`:

```ruby
VOWELS = %w[a e i o u]
case letter
when *VOWELS then "vowel"
else "consonant"
end
```

### `case` without a subject

Works like an `if/elsif` chain and lets each `when` be any condition:

```ruby
case
when temperature > 30 then "hot"
when temperature > 20 then "warm"
else "cool"
end
```

### `case` is an expression

```ruby
size = case weight
       when 0...5   then :small
       when 5...20  then :medium
       else              :large
       end
```

If nothing matches and there is no `else`, the result is `nil`. (Ruby also has a more powerful `case/in` for pattern matching, covered in Lesson 19. Do not mix `when` and `in` in one `case`.)

---

## 10.4 The Ternary Operator

`condition ? value_if_true : value_if_false` is a compact `if/else` for simple choices:

```ruby
age = 20
status = age >= 18 ? "adult" : "minor"
puts status           # adult

puts "You have #{count} #{count == 1 ? 'item' : 'items'}"
```

Guidelines:

- Great for short expressions assigned to a variable or interpolated into a string.
- **Do not nest ternaries**. Use `if/elsif` or `case`.
- Put spaces around `?` and `:`. With symbols, add a space before the `:` so Ruby does not read `x ?a :b` wrongly.

```ruby
puts x > 0 ? "positive" : x < 0 ? "negative" : "zero"    # works, but hard to read
```

---

## 10.5 Statement Modifiers (one-line if/unless)

For a short action, place `if` or `unless` **after** the statement:

```ruby
puts "Adult" if age >= 18
puts "Please log in" unless logged_in
raise ArgumentError, "name required" if name.nil?
return if items.empty?
next if line.strip.empty?       # inside loops
```

The same trick works for loops:

```ruby
puts "tick" while running
i += 1 until i >= 10
```

Guidelines:

- Use modifiers only for **single, short** statements. If the line gets long, use a regular block.
- Don't put a long block with a trailing modifier (`begin...end while` is the special do-while form, Lesson 11).
- Don't chain several modifiers on one line.

---

## 10.6 Conditionals Return Values

In Ruby, `if`, `unless`, and `case` are **expressions**. They return the value of the last expression in the branch that ran, or `nil` if no branch ran.

```ruby
message = if score >= 60
            "pass"
          else
            "fail"
          end

grade = case score
        when 90.. then "A"
        when 80.. then "B"
        else "C"
        end

result = (puts "hi" if false)
p result       # nil   (no branch ran)
```

This lets you assign the result directly, avoiding repeated assignments inside each branch:

```ruby
# Instead of:
if admin
  label = "Admin"
else
  label = "User"
end

# Write:
label = if admin then "Admin" else "User" end
```

Methods return their last expression, so a method body can simply be a conditional:

```ruby
def sign(n)
  if n > 0
    1
  elsif n < 0
    -1
  else
    0
  end
end

puts sign(-5)    # -1
```

---

[Previous](./[9]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[11]-Loops-and-Iteration.md)