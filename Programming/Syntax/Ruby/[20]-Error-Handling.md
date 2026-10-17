[Previous](./[19]-Pattern-Matching.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[21]-Regular-Expressions.md)

*Core Syntax*

# Lesson 20 - Error Handling & Exceptions (begin/rescue/ensure, raise, retry)

Things go wrong at runtime: files are missing, networks fail, input is invalid. Ruby reports these problems as **exceptions**. Handling them lets your program recover or fail gracefully.

---

## 20.1 What Is an Exception?

An **exception** is an object that represents an error. When something fails, Ruby **raises** an exception, stops normal execution, and looks up the call stack for code that **rescues** it. If nothing rescues it, the program ends and prints the error.

```ruby
puts 10 / 0
# ZeroDivisionError: divided by 0 (and the program stops)
```

An uncaught error prints a message and a **backtrace** (the chain of method calls that led to it):

```
script.rb:3:in 'Integer#/': divided by 0 (ZeroDivisionError)
	from script.rb:3:in 'divide'
	from script.rb:7:in '<main>'
```

Read it from the top: the first line says what happened and where. The lines below show who called whom.

Common exceptions:

| Exception | Typical cause |
|-----------|---------------|
| `ZeroDivisionError` | `1 / 0` |
| `NoMethodError` | Calling a method an object doesn't have (often on `nil`) |
| `NameError` | Using an undefined variable or constant |
| `ArgumentError` | Wrong number or value of arguments (`Integer("abc")`) |
| `TypeError` | Wrong type (`1 + "2"`) |
| `KeyError` | `hash.fetch(:missing)` |
| `IndexError` | `[1].fetch(5)` |
| `FrozenError` | Changing a frozen object |
| `Errno::ENOENT` | File not found |
| `RuntimeError` | Generic error from `raise "message"` |

---

## 20.2 begin / rescue / else / ensure

```ruby
begin
  result = 10 / 0
  puts "result: #{result}"
rescue ZeroDivisionError => e
  puts "Cannot divide by zero: #{e.message}"
else
  puts "No errors happened"
ensure
  puts "This always runs"
end
```

Output:

```
Cannot divide by zero: divided by 0
This always runs
```

| Clause | Runs when |
|--------|-----------|
| `begin` | The code that might fail |
| `rescue` | An exception of a matching class is raised |
| `else` | **No** exception was raised |
| `ensure` | **Always** (cleanup: closing files, unlocking, etc.) |

### Details about rescue

```ruby
begin
  Integer("abc")
rescue ArgumentError => e
  puts e.class       # ArgumentError
  puts e.message     # invalid value for Integer(): "abc"
  puts e.backtrace.first   # where it happened
  puts e.full_message(highlight: false)
end
```

Rescue several kinds, in order. The **first matching** `rescue` wins, so list specific errors before general ones:

```ruby
begin
  risky_operation
rescue ZeroDivisionError, TypeError => e     # either one
  puts "math or type problem: #{e.message}"
rescue ArgumentError
  puts "bad argument"
rescue => e                                  # any StandardError
  puts "other problem: #{e.class}"
end
```

A bare `rescue` (without a class) catches `StandardError` and its subclasses, which covers almost all everyday errors.

### Method-level rescue (no `begin` needed)

```ruby
def read_config(path)
  File.read(path)
rescue Errno::ENOENT
  puts "no config file, using defaults"
  ""
ensure
  puts "read_config finished"
end
```

The same works in `do ... end` blocks:

```ruby
[1, 0].each do |n|
  puts 10 / n
rescue ZeroDivisionError
  puts "skipped zero"
end
```

### The rescue modifier

For quick, one-line fallbacks (use sparingly, it hides which error occurred):

```ruby
number = Integer("abc") rescue 0
```

### `ensure` for cleanup

```ruby
file = File.open("data.txt", "w")
begin
  file.puts "hello"
ensure
  file.close      # runs even if puts raised
end

# Better: the block form closes the file for you
File.open("data.txt", "w") { |f| f.puts "hello" }
```

Don't use `return` inside `ensure`, because it silently swallows exceptions.

### The last exception: `$!`

Inside a `rescue`, `$!` holds the current exception (same as `e`).

---

## 20.3 raise and Re-raising

Use **`raise`** to signal an error yourself.

```ruby
raise "Something went wrong"                    # RuntimeError with a message
raise ArgumentError, "age must be positive"     # a specific class and message
raise ArgumentError.new("age must be positive") # same, building the object
raise ArgumentError                             # class only (default message = class name)
```

Typical validation:

```ruby
def withdraw(balance, amount)
  raise ArgumentError, "amount must be positive" unless amount.positive?
  raise "insufficient funds" if amount > balance
  balance - amount
end

begin
  withdraw(100, 500)
rescue => e
  puts "Error: #{e.message}"      # Error: insufficient funds
end
```

### Re-raising

Inside a `rescue`, a bare `raise` throws the **same** exception again. Use it when you want to log or clean up, but still let the caller decide:

```ruby
begin
  process_payment
rescue PaymentError => e
  logger.error("Payment failed: #{e.message}")
  raise                       # pass it along unchanged
end
```

### Wrapping errors and `cause`

If you raise a new exception inside a `rescue`, Ruby remembers the original as the **`cause`**:

```ruby
begin
  begin
    Integer("x")
  rescue ArgumentError
    raise "Invalid input"
  end
rescue => e
  puts e.message          # Invalid input
  puts e.cause.message    # invalid value for Integer(): "x"
end

raise MyError, "message", cause: original_error     # explicit cause
```

### Other tools

```ruby
warn "Deprecated"             # print to stderr, no exception
abort("fatal problem")        # print to stderr and exit with status 1
exit(1)                       # leave the program (raises SystemExit)
at_exit { puts "bye" }        # run code when the program ends
raise NotImplementedError, "#{self.class} must implement #{__method__}"
```

---

## 20.4 retry

Inside a `rescue`, **`retry`** jumps back to the start of the `begin` block to try again. Always put a **limit** on the attempts to avoid an infinite loop.

```ruby
attempts = 0

begin
  attempts += 1
  puts "Attempt #{attempts}"
  raise "network error" if attempts < 3
  puts "Success!"
rescue => e
  if attempts < 5
    sleep(0.1 * attempts)      # wait a little longer each time (backoff)
    retry
  else
    puts "Giving up: #{e.message}"
    raise
  end
end
```

Output:

```
Attempt 1
Attempt 2
Attempt 3
Success!
```

A reusable helper:

```ruby
def with_retries(max: 3, on: StandardError)
  tries = 0
  begin
    tries += 1
    yield tries
  rescue on => e
    retry if tries < max
    raise
  end
end

with_retries(max: 4, on: Errno::ECONNREFUSED) do |try|
  puts "calling API (try #{try})"
  call_api
end
```

Only retry errors that are likely to be **temporary** (timeouts, busy servers), not programming mistakes.

---

## 20.5 The Exception Hierarchy

All exceptions are objects of classes that inherit from `Exception`. The structure (simplified):

```
Exception
├── NoMemoryError
├── ScriptError
│   ├── LoadError
│   ├── NotImplementedError
│   └── SyntaxError
├── SecurityError
├── SignalException
│   └── Interrupt          (Ctrl+C)
├── SystemExit             (raised by exit)
├── SystemStackError       (too much recursion)
└── StandardError          <- the family you normally rescue
    ├── ArgumentError
    │   └── UncaughtThrowError
    ├── EncodingError
    ├── FiberError
    ├── IOError
    │   └── EOFError
    ├── IndexError
    │   ├── KeyError
    │   └── StopIteration
    ├── LocalJumpError
    ├── NameError
    │   └── NoMethodError
    ├── RangeError
    │   └── FloatDomainError
    ├── RegexpError
    ├── RuntimeError
    │   └── FrozenError
    ├── SystemCallError    (Errno::ENOENT, Errno::EACCES, ...)
    ├── ThreadError
    ├── TypeError
    └── ZeroDivisionError
```

See it yourself:

```ruby
p ZeroDivisionError.ancestors
# [ZeroDivisionError, StandardError, Exception, Object, ...]
p KeyError.superclass       # IndexError
```

### Why the hierarchy matters

- `rescue SomeClass` also catches **all subclasses**: `rescue IndexError` catches `KeyError` and `StopIteration`.
- A bare `rescue` catches only `StandardError`. This is on purpose, because the other branches (`SystemExit`, `Interrupt`, `NoMemoryError`, `SyntaxError`) are things your program should usually **not** swallow.
- **Do not `rescue Exception`**. It would catch Ctrl+C and `exit`, making the program impossible to stop.

```ruby
# Bad
begin
  run
rescue Exception
  # also hides SystemExit and Interrupt
end

# Good
begin
  run
rescue StandardError => e
  handle(e)
end
```

Also avoid empty rescues that hide errors silently:

```ruby
begin
  work
rescue => e
  # nothing here: the error vanishes and bugs become invisible
end
```

At least log the error, or let it propagate.

---

## 20.6 Custom Exception Classes

Create your own error types by inheriting from `StandardError` (or one of its subclasses). Named errors make code clearer and let callers rescue exactly what they care about.

```ruby
class InsufficientFundsError < StandardError; end

class BankAccount
  def initialize(balance)
    @balance = balance
  end

  def withdraw(amount)
    raise InsufficientFundsError, "need #{amount - @balance} more" if amount > @balance
    @balance -= amount
  end
end

begin
  BankAccount.new(50).withdraw(80)
rescue InsufficientFundsError => e
  puts "Declined: #{e.message}"      # Declined: need 30 more
end
```

### A default message

```ruby
class ValidationError < StandardError
  def initialize(message = "validation failed")
    super
  end
end

raise ValidationError             # ValidationError: validation failed
raise ValidationError, "bad name" # ValidationError: bad name
```

### Carrying extra data

```ruby
class HttpError < StandardError
  attr_reader :status

  def initialize(status, message = "HTTP #{status}")
    @status = status
    super(message)
  end
end

begin
  raise HttpError.new(404, "Not found")
rescue HttpError => e
  puts "#{e.status}: #{e.message}"     # 404: Not found
end
```

### A family of errors for a library or app

```ruby
module MyApp
  class Error < StandardError; end        # base class for everything in MyApp
  class NotFound < Error; end
  class Unauthorized < Error; end
end

begin
  raise MyApp::NotFound, "user 7"
rescue MyApp::Error => e                  # catches any MyApp error
  puts "#{e.class}: #{e.message}"
end
```

Guidelines:

- Name error classes ending in `Error`.
- Inherit from `StandardError` (never directly from `Exception`).
- Provide a clear message that explains **what** went wrong and, if possible, how to fix it.
- Define one base error per library so users can rescue the whole family.

---

## 20.7 catch and throw

`catch` and `throw` are a way to **jump out of deeply nested code** (not an error mechanism). `catch` sets a labeled landing spot; `throw` jumps to it.

```ruby
result = catch(:found) do
  (1..5).each do |i|
    (1..5).each do |j|
      throw :found, [i, j] if i * j == 12
    end
  end
  nil          # reached only if nothing was thrown
end

p result       # [3, 4]
```

- `catch(:label) { ... }` returns the value passed to `throw`, or the block's last value if no throw happened.
- The label can be a symbol or any object.
- `throw` with no matching `catch` raises `UncaughtThrowError`.

A use for searching nested data:

```ruby
def find_first_negative(matrix)
  catch(:done) do
    matrix.each { |row| row.each { |n| throw :done, n if n < 0 } }
    nil
  end
end

p find_first_negative([[1, 2], [3, -4], [-5]])    # -4
```

### Choosing the right tool

| Situation | Use |
|-----------|-----|
| Something is wrong | `raise` / `rescue` |
| Exit one loop | `break` |
| Exit a method early | `return` |
| Exit several nested loops | `catch` / `throw` (or extract a method and `return`) |

---

[Previous](./[19]-Pattern-Matching.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[21]-Regular-Expressions.md)