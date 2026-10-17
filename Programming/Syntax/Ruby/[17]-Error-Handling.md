[Previous](./[16]-Inheritance-And-Modules.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[18]-Files-And-IO.md)

*Working With Ruby*

# Lesson 17 - Error Handling

## 17.1 What Are Exceptions?

An exception is an object that represents an error. When one is raised, normal execution stops and Ruby looks for a matching `rescue`. If none is found, the program ends with a message and a backtrace.

```ruby
10 / 0            # ZeroDivisionError: divided by 0
nil.upcase        # NoMethodError: undefined method 'upcase' for nil
Integer("abc")    # ArgumentError: invalid value for Integer(): "abc"
[].fetch(5)       # IndexError: index 5 outside of array bounds
{ a: 1 }.fetch(:b)# KeyError: key not found: :b
"a" + 1           # TypeError: no implicit conversion of Integer into String
File.read("nope") # Errno::ENOENT: No such file or directory
```

---

## 17.2 begin / rescue / else / ensure

```ruby
begin
  result = Integer(input)
rescue ArgumentError => e
  puts "Bad number: #{e.message}"
  result = 0
rescue TypeError, NoMethodError => e     # several classes in one clause
  puts "Wrong type: #{e.class}"
else
  puts "Parsed OK"                       # runs only if nothing was raised
ensure
  puts "Always runs"                     # cleanup: runs no matter what
end
```

- `rescue` without a class rescues `StandardError` and its subclasses.
- `else` runs only when the `begin` body raised nothing.
- `ensure` always runs, even after `return`, `break`, or an uncaught exception.

`def`, `do...end` blocks, and classes can have `rescue` directly, with no `begin`:

```ruby
def read_config(path)
  File.read(path)
rescue Errno::ENOENT
  "{}"
ensure
  puts "done"
end
```

The inline modifier handles simple cases:

```ruby
value = Integer("x") rescue 0      # rescues StandardError only; use sparingly
```

---

## 17.3 Raising Exceptions

```ruby
raise "Something went wrong"                  # RuntimeError
raise ArgumentError                           # default message
raise ArgumentError, "age must be positive"
raise ArgumentError.new("age must be positive")

def withdraw(amount)
  raise ArgumentError, "amount must be positive" unless amount.positive?
  raise InsufficientFunds, "balance too low" if amount > @balance
  @balance -= amount
end
```

Re-raise the current exception with a bare `raise`, and keep the original error as the `cause`:

```ruby
begin
  risky
rescue => e
  logger.error(e.message)
  raise                                   # re-raise the same error
end

begin
  parse(data)
rescue JSON::ParserError => e
  raise ConfigError, "bad config"        # e is stored automatically as #cause
end
```

---

## 17.4 Exception Hierarchy

```
Exception
 ├── NoMemoryError
 ├── ScriptError
 │    ├── LoadError
 │    ├── NotImplementedError
 │    └── SyntaxError
 ├── SecurityError
 ├── SignalException
 │    └── Interrupt
 ├── SystemExit
 ├── SystemStackError
 └── StandardError           ← what a bare `rescue` catches
      ├── ArgumentError
      │    └── UncaughtThrowError
      ├── EncodingError
      ├── FiberError
      ├── IOError
      │    └── EOFError
      ├── IndexError
      │    ├── KeyError
      │    └── StopIteration
      ├── LocalJumpError
      ├── NameError
      │    └── NoMethodError
      ├── RangeError
      │    └── FloatDomainError
      ├── RegexpError
      ├── RuntimeError
      │    └── FrozenError
      ├── SystemCallError    (Errno::*)
      ├── ThreadError
      ├── TypeError
      └── ZeroDivisionError
```

Never `rescue Exception` (it also catches `Interrupt` and `SystemExit`, so `Ctrl+C` stops working). Rescue the most specific class you can handle.

---

## 17.5 Custom Exceptions

Inherit from `StandardError` (or a more specific class):

```ruby
class AppError < StandardError; end
class ValidationError < AppError; end

class InsufficientFunds < AppError
  attr_reader :needed

  def initialize(needed, msg = "insufficient funds: need #{needed} more")
    @needed = needed
    super(msg)
  end
end

begin
  raise InsufficientFunds.new(25)
rescue AppError => e         # catches all AppError subclasses
  puts e.message             #=> insufficient funds: need 25 more
  puts e.needed              #=> 25
end
```

Group related errors under one base class so callers can rescue broadly or narrowly.

---

## 17.6 Inspecting Exceptions

```ruby
begin
  raise ArgumentError, "bad"
rescue => e
  e.class                  #=> ArgumentError
  e.message                #=> "bad"
  e.full_message           # message + backtrace, formatted
  e.backtrace.first(3)     # array of "file:line:in 'method'"
  e.backtrace_locations
  e.cause                  # the exception being handled when this one was raised
  e.is_a?(StandardError)   #=> true
end

caller              # current call stack as an array of strings
$!                  # the exception currently being handled
```

`NameError` and `NoMethodError` carry `#name` and `#receiver`. With the `did_you_mean` gem (enabled by default) Ruby suggests corrections for typos.

---

## 17.7 retry, catch/throw, And Exiting

`retry` re-runs the `begin` block:

```ruby
attempts = 0
begin
  attempts += 1
  fetch_data
rescue Timeout::Error
  retry if attempts < 3
  raise
end
```

`catch`/`throw` is non-error control flow for escaping nested code (see Lesson 9).

Exiting a program:

```ruby
exit            # raises SystemExit, runs ensure blocks and at_exit handlers
exit 1          # non-zero status = failure
abort("fatal")  # prints message to stderr and exits with status 1
exit!           # exit immediately, skipping handlers
at_exit { puts "cleanup" }
warn "deprecated"   # print to stderr
```

---

## 17.8 Good Practices

- Rescue only what you can handle; let the rest bubble up.
- Don't silently swallow errors (`rescue; end`). At least log them.
- Use `ensure` (or the block form of `File.open`) to release resources.
- Prefer specific classes and meaningful messages.
- Raise for exceptional conditions, not ordinary flow; return `nil` or use `Integer(x, exception: false)` where failure is expected.
- Use `Timeout.timeout(5) { ... }` sparingly; it can interrupt code at unsafe points.
- Do not use `rescue Exception`, and avoid bare `rescue` around huge blocks.

---

[Previous](./[16]-Inheritance-And-Modules.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[18]-Files-And-IO.md)
