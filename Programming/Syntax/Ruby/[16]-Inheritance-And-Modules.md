[Previous](./[15]-Classes-And-Objects.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[17]-Error-Handling.md)

*Object-Oriented Ruby*

# Lesson 16 - Inheritance And Modules

## 16.1 Inheritance

A class can inherit from one **superclass** with `<`. The subclass gets all its methods and can override them.

```ruby
class Animal
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def speak = "..."
  def to_s = "#{self.class.name}: #{name}"
end

class Dog < Animal
  def initialize(name, breed)
    super(name)            # call the parent's initialize
    @breed = breed
  end

  def speak = "Woof"
end

class Puppy < Dog
  def speak = super + "!"  # call the parent version, then extend it
end

pup = Puppy.new("Rex", "Lab")
puts pup.speak             #=> Woof!
puts pup                   #=> Puppy: Rex
pup.is_a?(Animal)          #=> true
Puppy.superclass           #=> Dog
Puppy.ancestors            #=> [Puppy, Dog, Animal, Object, Kernel, BasicObject]
```

`super` forms:

- `super` (no parentheses) passes the **same arguments** the current method received.
- `super()` passes **no** arguments.
- `super(a, b)` passes exactly those arguments.

Everything inherits from `Object` (which inherits from `BasicObject`). Ruby has **single inheritance**; use modules to share behavior across unrelated classes.

---

## 16.2 Method Lookup

When you call `obj.method`, Ruby searches the **ancestors** chain in order and uses the first match.

```ruby
Puppy.ancestors
```

For singleton methods, Ruby first checks the object's singleton class. If nothing matches, Ruby calls `method_missing` (Lesson 21), which by default raises `NoMethodError`.

Understanding the chain explains why `super` works and how modules fit in.

---

## 16.3 Modules As Namespaces

A module groups related constants, methods, and classes and avoids name collisions. Unlike a class, a module cannot be instantiated.

```ruby
module Geometry
  PI_APPROX = 3.14

  def self.circle_area(r) = PI_APPROX * r**2

  class Circle
    def initialize(r) = @r = r
    def area = Geometry.circle_area(@r)
  end
end

Geometry.circle_area(2)       #=> 12.56
Geometry::Circle.new(1).area
Geometry::PI_APPROX
```

`module_function` makes methods callable as `Module.method` and as private mixin methods:

```ruby
module Util
  module_function
  def slugify(s) = s.downcase.strip.gsub(/\s+/, "-")
end
Util.slugify("Hello World")   #=> "hello-world"
```

---

## 16.4 Mixins: include, extend, prepend

A module's instance methods can be mixed into classes.

```ruby
module Walkable
  def walk = "#{name} walks"
end

module Swimmable
  def swim = "#{name} swims"
end

class Duck
  include Walkable
  include Swimmable
  attr_reader :name
  def initialize(name) = @name = name
end

d = Duck.new("Don")
d.walk                       #=> "Don walks"
Duck.ancestors.first(3)      #=> [Duck, Swimmable, Walkable]
Duck.include?(Walkable)      #=> true
d.is_a?(Swimmable)           #=> true
```

| Keyword | Effect |
| --- | --- |
| `include M` | Adds M's methods as **instance** methods; M sits **after** the class in the chain |
| `prepend M` | Adds M's methods as instance methods; M sits **before** the class, so it can wrap methods |
| `extend M` | Adds M's methods as **class** (singleton) methods |

```ruby
module Loud
  def speak = super.upcase + "!"
end

class Cat
  prepend Loud
  def speak = "meow"
end
Cat.new.speak   #=> "MEOW!"

module ClassHelpers
  def create(*args) = new(*args)
end

class Widget
  extend ClassHelpers
end
Widget.create

obj = Object.new
obj.extend(Walkable)   # mix into one object only
```

Hooks run when a module is included, extended, or prepended:

```ruby
module Trackable
  def self.included(base)
    base.extend(ClassMethods)
  end

  module ClassMethods
    def tracked = true
  end
end
```

---

## 16.5 Built-In Mixins: Comparable And Enumerable

The two most important core mixins:

```ruby
class Money
  include Comparable
  attr_reader :cents
  def initialize(cents) = @cents = cents
  def <=>(other) = cents <=> other.cents
end

Money.new(5) < Money.new(10)   #=> true
[Money.new(9), Money.new(2)].min.cents   #=> 2
```

`Enumerable` (Lesson 12) needs only `each`. `Kernel` is mixed into `Object` and provides `puts`, `require`, `raise`, `format`, and many other global functions.

---

## 16.6 Polymorphism And Duck Typing

Different objects respond to the same message in their own way:

```ruby
class Circle; def area = 3.14 * 2 * 2; end
class Square; def area = 4 * 4; end

[Circle.new, Square.new].each { |shape| puts shape.area }
```

Ruby does not require a common superclass. If an object responds to the method, it works ("if it walks like a duck..."). Check with `respond_to?` when needed instead of checking the class.

An abstract method pattern:

```ruby
class Shape
  def area
    raise NotImplementedError, "#{self.class} must implement area"
  end
end
```

---

## 16.7 Composition Over Inheritance

Inheritance models "is a" relationships; composition models "has a" and is more flexible.

```ruby
class Engine
  def start = "vroom"
end

class Car
  def initialize(engine: Engine.new) = @engine = engine
  def start = @engine.start
end
```

Use delegation to forward methods:

```ruby
require "forwardable"

class Playlist
  extend Forwardable
  def_delegators :@songs, :size, :each, :<<
  include Enumerable

  def initialize = @songs = []
end
```

Guidelines: keep hierarchies shallow, prefer modules for shared behavior, and avoid inheriting from core classes such as `Array` or `Hash` (their methods often return base-class instances rather than your subclass).

---

## 16.8 Useful Class And Module Reflection

```ruby
Dog.superclass               #=> Animal
Dog.ancestors
Dog.instance_methods(false)
Dog.method_defined?(:speak)
Dog.subclasses               #=> [Puppy]
Module.new                   # anonymous module
Class.new(Animal) { def speak = "dynamic" }
Dog <= Animal                #=> true  (subclass check)
Walkable === d               #=> true
Dog.instance_of?(Class)      #=> true
```

Hook methods `inherited(subclass)` and `included(base)` let a class or module react when it is subclassed or mixed in.

```ruby
class Plugin
  @registry = []
  class << self
    attr_reader :registry
    def inherited(sub)
      super
      Plugin.registry << sub
    end
  end
end
```

---

[Previous](./[15]-Classes-And-Objects.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[17]-Error-Handling.md)
