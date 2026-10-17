[Previous](./[20]-Testing-And-Debugging.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md)

*Going Further*

# Lesson 21 - Advanced Ruby

## 21.1 The Ruby Object Model

Everything is an object, including classes. A class is an instance of `Class`, and `Class` is a subclass of `Module`.

```ruby
5.class                #=> Integer
Integer.class          #=> Class
Class.superclass       #=> Module
Module.superclass      #=> Object
Object.superclass      #=> BasicObject
BasicObject.superclass #=> nil
```

Every object can have a **singleton class** (also called eigenclass) that holds methods for just that object. Class methods are singleton methods of the class object.

```ruby
str = "hello"
def str.shout = upcase + "!"
str.singleton_class.instance_methods(false)   #=> [:shout]

class Foo
  class << self
    def build = new      # defined on Foo's singleton class
  end
end
```

Method lookup goes: singleton class, prepended modules, the class, included modules (last included first), then the superclass chain.

---

## 21.2 Dynamic Dispatch And Reflection

```ruby
obj = "text"

obj.send(:upcase)                # call a method by name (even private)
obj.public_send(:length)         # respects visibility
obj.respond_to?(:upcase)
obj.method(:upcase).call
obj.public_methods(false)
String.instance_methods(false).size

class Config
  def initialize = @data = { host: "x", port: 1 }
end
c = Config.new
c.instance_variable_set(:@debug, true)
c.instance_variable_get(:@debug)       #=> true
c.instance_variables                   #=> [:@data, :@debug]
Config.class_variable_defined?(:@@x)
Object.const_get(:Config)
Object.const_set(:ANSWER, 42)
```

---

## 21.3 Defining Methods Dynamically

```ruby
class Person
  %w[name email phone].each do |field|
    define_method(field) { @attrs[field] }
    define_method("#{field}=") { |value| @attrs[field] = value }
    define_method("#{field}?") { !@attrs[field].nil? }
  end

  def initialize = @attrs = {}
end

pr = Person.new
pr.name = "Ada"
pr.name       #=> "Ada"
pr.email?     #=> false
```

`method_missing` handles calls to methods that do not exist. Always pair it with `respond_to_missing?` and call `super` for unknown names:

```ruby
class Ghost
  def method_missing(name, *args, &block)
    if name.to_s.start_with?("get_")
      "value of #{name.to_s.delete_prefix('get_')}"
    else
      super
    end
  end

  def respond_to_missing?(name, include_private = false)
    name.to_s.start_with?("get_") || super
  end
end

g = Ghost.new
g.get_color            #=> "value of color"
g.respond_to?(:get_x)  #=> true
g.unknown              # NoMethodError
```

Prefer `define_method` when the set of methods is known: it is faster and easier to debug.

---

## 21.4 Evaluating Code In Other Contexts

```ruby
class Thing; end

Thing.class_eval do              # runs with self = Thing; def adds instance methods
  def hello = "hello"
end

obj = Thing.new
obj.instance_eval { @secret = 42 }   # runs with self = obj
obj.instance_variable_get(:@secret)  #=> 42

Thing.instance_exec(5) { |n| puts n + 1 }  # pass arguments to the block
eval("1 + 2")                   # evaluates a string (avoid with untrusted input!)
```

`instance_eval` powers internal **DSLs** (domain-specific languages):

```ruby
class HTMLBuilder
  def initialize = @out = []
  def build(&block)
    instance_eval(&block)
    @out.join("\n")
  end

  %w[h1 p].each do |tag|
    define_method(tag) { |text| @out << "<#{tag}>#{text}</#{tag}>" }
  end
end

puts HTMLBuilder.new.build {
  h1 "Title"
  p "Body"
}
```

Never `eval` or `send` anything derived from user input.

---

## 21.5 Hooks And Callbacks

Ruby calls special methods when structural events happen:

```ruby
class Base
  def self.inherited(subclass)
    super
    puts "#{subclass} inherits from #{self}"
  end

  def self.method_added(name)
    puts "added #{name}"
  end
end

module Plugin
  def self.included(base) = puts("#{self} included in #{base}")
  def self.extended(base) = puts("extended #{base}")
  def self.prepended(base) = puts("prepended to #{base}")
end

class Child < Base
  include Plugin
  def run; end
end
```

Other hooks: `const_missing`, `singleton_method_added`, `method_removed`, `Module#prepend_features`, `ObjectSpace.define_finalizer`, and `Kernel#at_exit`.

---

## 21.6 Refinements

Refinements change core classes only within a specific scope, avoiding the dangers of global monkey patching.

```ruby
module StringExtras
  refine String do
    def shout = upcase + "!"
  end
end

# "hi".shout            # NoMethodError here

using StringExtras      # active from here to the end of the file (or module)
puts "hi".shout         #=> HI!
```

---

## 21.7 Concurrency: Threads, Fibers, And Ractors

Ruby (CRuby) has a **Global VM Lock (GVL)**: only one thread runs Ruby code at a time, but threads still help with I/O-bound work (network, files, sleeping), because the lock is released while waiting.

**Threads:**

```ruby
threads = 3.times.map do |i|
  Thread.new(i) do |n|
    sleep(0.1)
    "result #{n}"
  end
end
puts threads.map(&:value)       # value waits for each thread to finish

counter = 0
lock = Mutex.new
10.times.map { Thread.new { 1000.times { lock.synchronize { counter += 1 } } } }.each(&:join)
counter   #=> 10000

queue = Queue.new
producer = Thread.new { 3.times { |i| queue << i }; queue << :done }
consumer = Thread.new do
  while (item = queue.pop) != :done
    puts "got #{item}"
  end
end
[producer, consumer].each(&:join)
```

Thread-related tools: `Thread.current`, thread-local variables (`Thread.current[:key]`), `Mutex`, `Queue`, `SizedQueue`, `ConditionVariable`, and `Monitor`. Exceptions in threads are raised when you call `join` or `value`.

**Fibers** are lightweight, cooperatively scheduled units of execution that you pause and resume explicitly:

```ruby
fiber = Fiber.new do
  puts "step 1"
  Fiber.yield 1
  puts "step 2"
  Fiber.yield 2
  :finished
end

fiber.resume   # prints "step 1", returns 1
fiber.resume   # prints "step 2", returns 2
fiber.resume   #=> :finished
```

Ruby also has a **Fiber Scheduler** interface that gives non-blocking I/O to ordinary-looking code (libraries such as `async` implement it). `Enumerator` external iteration (`next`) is built on fibers.

**Ractors** run in parallel with separate object spaces, so they are not limited by the GVL. They communicate by passing messages and may only share frozen (shareable) objects. Ractors are still marked experimental, and print a warning on first use.

```ruby
r = Ractor.new { (1..1_000).sum }
puts r.value               #=> 500500  (Ractor#value waits for the result)

port = Ractor::Port.new    # a message channel (Ruby 4.0+)
worker = Ractor.new(port) do |out|
  3.times { |i| out << i * 10 }
end
3.times { puts port.receive }
```

In Ruby 4.0, `Ractor::Port` replaced the older `Ractor.yield` / `Ractor#take` model.

**Processes** give true parallelism by copying the program:

```ruby
pid = fork { puts "child #{Process.pid}" }   # Unix only
Process.wait(pid)
```

Which tool to pick: threads for I/O waiting, fibers/async for many concurrent connections, processes (or Ractors) for CPU-heavy work.

---

## 21.8 Memory, Garbage Collection, And Performance

```ruby
GC.start                      # request a collection
GC.stat[:count]               # collection statistics
GC.disable; GC.enable
ObjectSpace.memsize_of("hello")   # require "objspace" first
ObjectSpace.each_object(String).count
"abc".frozen?                 # freezing and deduplication save memory
-"abc"                        # unary minus returns a deduplicated frozen string
```

CRuby uses a generational, incremental, compacting garbage collector. Performance tips:

- Avoid creating needless objects in hot loops; use `frozen_string_literal: true`.
- Prefer `Symbol#to_proc` and built-in iterators over manual loops.
- Use `Set` or `Hash` for lookups instead of searching arrays.
- Use `each_slice`, `lazy`, and `File.foreach` for large data.
- Profile before optimizing.

JIT compilers speed up long-running programs:

```bash
ruby --yjit script.rb         # YJIT: production-ready JIT
ruby --zjit script.rb         # ZJIT: new, experimental method JIT (Ruby 4.0), if built with it
RUBY_YJIT_ENABLE=1 ruby app.rb
```

Alternative implementations exist: **JRuby** (on the JVM), **TruffleRuby** (GraalVM), and **mruby** (embedded).

---

## 21.9 Ruby::Box (Experimental)

Ruby 4.0 introduced `Ruby::Box`, an experimental feature that creates isolated spaces inside one Ruby process. Definitions made in one box (such as monkey patches, global variables, and constants) do not leak into others, which helps with testing, running several versions of a library, and safer plugin systems.

```ruby
# Start Ruby with the feature turned on:  RUBY_BOX=1 ruby app.rb
box = Ruby::Box.new
box.eval("class String; def shout = upcase + '!'; end")
box.eval("'hi'.shout")      #=> "HI!"
"hi".respond_to?(:shout)    #=> false  (the main box is untouched)
```

Because the API is experimental, check the current documentation (`doc/language/box.md` in the Ruby source) before relying on details.

---

## 21.10 Where To Go Next

- **Web development**: learn Ruby on Rails (full-stack framework) or Sinatra (a minimal one); understand HTTP, Rack, and SQL.
- **Scripting and tooling**: build command-line tools with `OptionParser` or Thor; automate with Rake.
- **Testing**: master RSpec or Minitest and practice TDD.
- **Language depth**: read the official documentation, study the standard library source, and explore *Metaprogramming Ruby* style techniques.
- **Practice**: solve small problems (Exercism, Advent of Code, Codewars), contribute to open-source gems, and read well-written Ruby code.
- **Community**: Ruby Weekly newsletter, RubyConf, the official Ruby mailing lists, and local meetups.

Congratulations on completing the Ruby course!

---

[Previous](./[20]-Testing-And-Debugging.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md)
