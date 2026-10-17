[Previous](./[19]-Gems-Bundler-And-The-Standard-Library.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[21]-Advanced-Ruby.md)

*Working With Ruby*

# Lesson 20 - Testing And Debugging

## 20.1 Why Test?

Automated tests prove that code works, catch regressions when you change it, and document intended behavior. A good test is small, fast, independent, and checks one behavior. A common structure is **Arrange, Act, Assert**: set up data, run the code, check the outcome.

Ruby has two main frameworks: **Minitest** (bundled with Ruby, simple and fast) and **RSpec** (a popular gem with a descriptive DSL).

---

## 20.2 Minitest

Code under test (`calculator.rb`):

```ruby
class Calculator
  def add(a, b) = a + b
  def divide(a, b)
    raise ZeroDivisionError, "cannot divide by zero" if b.zero?
    a.fdiv(b)
  end
end
```

Test file (`calculator_test.rb`):

```ruby
require "minitest/autorun"
require_relative "calculator"

class CalculatorTest < Minitest::Test
  def setup
    @calc = Calculator.new          # runs before each test
  end

  def teardown
    # runs after each test
  end

  def test_add
    assert_equal 5, @calc.add(2, 3)
  end

  def test_divide
    assert_in_delta 3.333, @calc.divide(10, 3), 0.001
  end

  def test_divide_by_zero
    error = assert_raises(ZeroDivisionError) { @calc.divide(1, 0) }
    assert_match(/zero/, error.message)
  end

  def test_skipped
    skip "not implemented yet"
  end
end
```

Run it:

```bash
ruby calculator_test.rb
ruby calculator_test.rb -n test_add        # one test
ruby calculator_test.rb -v                 # verbose
bundle exec rake test                      # with a Rakefile
```

Common assertions:

| Assertion | Checks |
| --- | --- |
| `assert(x)` / `refute(x)` | Truthy / falsy |
| `assert_equal(expected, actual)` | `==` (expected comes first) |
| `assert_nil(x)` | `nil` |
| `assert_includes(list, item)` | Membership |
| `assert_instance_of(Class, obj)` | Exact class |
| `assert_kind_of(Class, obj)` | Class or ancestor |
| `assert_raises(Err) { }` | An exception is raised |
| `assert_in_delta(a, b, d)` | Floats are close |
| `assert_match(regex, str)` | Regex match |
| `assert_output(out, err) { }` | Printed output |
| `assert_predicate(obj, :empty?)` | A predicate returns true |

Spec style is also available:

```ruby
require "minitest/autorun"

describe Calculator do
  let(:calc) { Calculator.new }

  it "adds numbers" do
    _(calc.add(2, 3)).must_equal 5
  end
end
```

Stubbing and mocking use `Minitest::Mock` and the `stub` method (`require "minitest/mock"`):

```ruby
Time.stub :now, Time.at(0) do
  assert_equal 1970, Time.now.year
end
```

---

## 20.3 RSpec

```bash
gem install rspec        # or add gem "rspec" to the Gemfile
rspec --init             # creates .rspec and spec/spec_helper.rb
rspec                    # run all specs
rspec spec/calculator_spec.rb:12   # run the example on line 12
```

```ruby
# spec/calculator_spec.rb
require_relative "../calculator"

RSpec.describe Calculator do
  subject(:calc) { described_class.new }

  describe "#add" do
    it "adds two numbers" do
      expect(calc.add(2, 3)).to eq(5)
    end
  end

  describe "#divide" do
    context "when dividing by zero" do
      it "raises an error" do
        expect { calc.divide(1, 0) }.to raise_error(ZeroDivisionError, /zero/)
      end
    end

    it "returns a float" do
      expect(calc.divide(10, 4)).to be_within(0.001).of(2.5)
    end
  end

  before { @started = true }
end
```

Frequently used matchers: `eq`, `eql`, `be`, `be_nil`, `be_truthy`, `be_a(Class)`, `include`, `match`, `start_with`, `be > 3`, `change { }.by(1)`, `raise_error`, `have_attributes`, `contain_exactly`, `all`.

Test doubles:

```ruby
user = double("User", name: "Ada")
allow(user).to receive(:save).and_return(true)
expect(user).to receive(:notify).with("hi").once
```

---

## 20.4 Testing Practices

- **TDD**: write a failing test, make it pass, then refactor ("red, green, refactor").
- Test **behavior**, not private implementation details.
- Keep tests independent: no shared mutable state or ordering assumptions.
- Use fixtures or factories to build data; use doubles only for slow or external dependencies.
- Measure coverage with SimpleCov, but remember that coverage shows what ran, not what was verified.
- Run tests automatically in CI (GitHub Actions, GitLab CI, etc.).

---

## 20.5 Debugging With Output

```ruby
p variable                     # shows inspect output and returns the value
pp complex_structure
puts "x=#{x.inspect}"
warn "debug: #{x}"             # goes to stderr
puts caller.first(5)           # see how this line was reached
puts __method__.inspect
```

Reading a backtrace: the first line names the error and the location where it happened; following lines show the chain of calls that led to it. Start with the first line that points to **your** code.

```
app.rb:5:in 'divide': divided by 0 (ZeroDivisionError)
	from app.rb:9:in 'main'
	from app.rb:12:in '<main>'
```

Ruby warns about suspicious code when you run `ruby -w`. Check syntax with `ruby -c file.rb`.

---

## 20.6 Interactive Debuggers

**`binding.irb`** opens an IRB session at that point in your code, with access to local variables:

```ruby
def total(items)
  sum = 0
  items.each do |i|
    binding.irb       # inspect i, sum, items; type exit to continue
    sum += i.price
  end
  sum
end
```

**The `debug` gem** (bundled with Ruby) is a full debugger:

```ruby
def calculate(x)
  debugger            # or binding.break; pauses execution here
  x * 2
end
```

```bash
rdbg script.rb             # same, using the debug gem's launcher
```

Debugger commands: `next` (step over), `step` (step into), `continue`, `finish` (run until the current method returns), `break file:line`, `info` (locals), `backtrace`, `p expr`, `watch @var`, `quit`.

Other tools: **pry** (an enhanced REPL), **byebug** (older debugger), **IRB's** `show_source`, `ls`, `cd` commands, and `ObjectSpace` utilities:

```ruby
ObjectSpace.each_object(Class).count
ObjectSpace.count_objects
```

---

## 20.7 Code Quality And Performance

**RuboCop** checks style and common bugs:

```bash
gem install rubocop
rubocop                 # report offenses
rubocop -a              # auto-correct safe offenses
rubocop -A              # auto-correct all (review the diff)
```

Configuration lives in `.rubocop.yml`.

**Benchmarking and profiling:**

```ruby
require "benchmark"

Benchmark.bm(7) do |x|
  x.report("map:")  { (1..1_000_000).map { |n| n * 2 } }
  x.report("each:") { a = []; (1..1_000_000).each { |n| a << n * 2 } }
end
```

- `benchmark-ips` (a gem) measures iterations per second.
- `stackprof` and `ruby-prof` find slow methods.
- `memory_profiler` finds allocation hot spots.
- `ruby --yjit` enables the YJIT JIT compiler for faster execution.
- Measure first, then optimize: most slowness comes from a few hot spots, often database queries or repeated work.

Good habits for fewer bugs: use `frozen_string_literal: true`, prefer small methods, name things clearly, handle `nil` explicitly, and read error messages carefully, since Ruby's messages and `did_you_mean` suggestions usually point straight at the problem.

---

[Previous](./[19]-Gems-Bundler-And-The-Standard-Library.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[21]-Advanced-Ruby.md)
