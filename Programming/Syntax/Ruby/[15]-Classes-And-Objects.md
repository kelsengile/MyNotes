[Previous](./[14]-Blocks-Procs-And-Lambdas.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[16]-Inheritance-And-Modules.md)

*Object-Oriented Ruby*

# Lesson 15 - Classes And Objects

## 15.1 Defining A Class

A class is a blueprint for objects. `new` allocates an object and calls `initialize`.

```ruby
class Dog
  def initialize(name, age)
    @name = name         # instance variables start with @
    @age = age
  end

  def speak
    "#{@name} says Woof!"
  end

  def birthday!
    @age += 1
    self                 # return self to allow chaining
  end

  def to_s
    "#{@name} (#{@age})"
  end
end

rex = Dog.new("Rex", 3)
puts rex.speak           #=> Rex says Woof!
puts rex.birthday!       #=> Rex (4)
puts "Dog: #{rex}"       # to_s is used by interpolation and puts
p rex                    # inspect shows the instance variables
```

Class names are constants in `PascalCase`. Instance variables are private to the object: outside code can only reach them through methods.

---

## 15.2 Accessors

```ruby
class Person
  attr_reader :name           # defines name
  attr_writer :nickname       # defines nickname=
  attr_accessor :age          # defines both age and age=

  def initialize(name, age)
    @name = name
    @age = age
  end
end

ada = Person.new("Ada", 36)
ada.name              #=> "Ada"
ada.age = 37
ada.age               #=> 37
```

Custom setters can validate input. Inside the class, assign through `self.` to call the setter rather than creating a local variable:

```ruby
class Temperature
  attr_reader :degrees

  def degrees=(value)
    raise ArgumentError, "below absolute zero" if value < -273.15
    @degrees = value
  end

  def reset
    self.degrees = 0     # calls the setter
  end
end
```

---

## 15.3 self, Class Methods, And Class State

`self` is the current object. Inside a class body it is the class itself.

```ruby
class Counter
  @instances = 0              # class-level instance variable
  MAX = 100                   # constant

  class << self
    attr_reader :instances    # accessor on the class
  end

  def self.create             # class method
    @instances += 1
    new
  end
end

Counter.create
Counter.instances   #=> 1
Counter::MAX        #=> 100
```

Class variables (`@@count`) are shared between a class and all its subclasses, which often causes surprises. Prefer class-level instance variables as above.

Alternative styles for class methods:

```ruby
class Util
  def self.helper = "a"
  class << self
    def another = "b"
  end
end
```

---

## 15.4 Visibility

```ruby
class Account
  def initialize(balance) = @balance = balance

  def >(other) = balance > other.balance   # protected is callable from same class

  def deposit(amount)
    validate(amount)
    @balance += amount
  end

  protected

  def balance = @balance

  private

  def validate(amount)
    raise ArgumentError, "must be positive" unless amount.positive?
  end
end
```

| Level | Who can call it |
| --- | --- |
| `public` (default) | Anyone |
| `protected` | Instances of the same class (or subclasses) |
| `private` | Only the object itself, with an implicit receiver (and `self.` receiver in modern Ruby) |

```ruby
private def helper; end          # applies to one method
private :a, :b                   # make existing methods private
private_class_method :new        # e.g. for singletons
private_constant :SECRET         # hide a constant
```

Ruby's privacy is not security: `obj.send(:private_method)` still works.

---

## 15.5 Object Equality, Comparison, And Display

```ruby
class Point
  attr_reader :x, :y
  def initialize(x, y) = (@x = x; @y = y)

  def ==(other)
    other.is_a?(Point) && x == other.x && y == other.y
  end
  alias eql? ==

  def hash = [x, y].hash        # keep consistent with eql?

  def to_s = "(#{x}, #{y})"
  def inspect = "#<Point #{x},#{y}>"
  def +(other) = Point.new(x + other.x, y + other.y)
  def [](i) = [x, y][i]
  def <=>(other) = [x, y] <=> [other.x, other.y]
  def to_a = [x, y]
end

Point.new(1, 2) == Point.new(1, 2)       #=> true
{ Point.new(1, 2) => :a }[Point.new(1, 2)] #=> :a
```

By default `==` compares object identity; override it (plus `hash` and `eql?`) for value equality. `to_s` is the friendly string; `inspect` is for debugging.

---

## 15.6 Structs And Data

For simple value containers, avoid boilerplate with `Struct` or `Data`.

```ruby
Point = Struct.new(:x, :y) do
  def distance = Math.sqrt(x**2 + y**2)
end

pt = Point.new(3, 4)
pt.x             #=> 3
pt.x = 10        # Struct objects are mutable
pt.to_a          #=> [10, 4]
pt == Point.new(10, 4)   #=> true

Pt = Struct.new(:x, :y, keyword_init: true)
Pt.new(x: 1, y: 2)
```

`Data` (Ruby 3.2+) creates **immutable** value objects:

```ruby
Coord = Data.define(:lat, :lng) do
  def to_s = "#{lat},#{lng}"
end

c = Coord.new(lat: 1.5, lng: 2.5)   # or Coord.new(1.5, 2.5)
c.lat                                #=> 1.5
c2 = c.with(lat: 9.0)                # copy with a changed field
c.frozen?                            #=> true
c.to_h                               #=> {lat: 1.5, lng: 2.5}
```

Both work with pattern matching and have `==`, `to_h`, and `inspect` ready to use.

---

## 15.7 Copying, Freezing, And Singleton Methods

```ruby
a = "text"
b = a.dup       # new object, unfrozen, no singleton methods
c = a.clone     # new object, keeps frozen state and singleton methods
a.freeze.clone(freeze: false)

obj = Object.new
def obj.special = "only me"     # singleton method
obj.special                     #=> "only me"
obj.singleton_methods           #=> [:special]
```

Customize copying with `initialize_copy`:

```ruby
class Doc
  def initialize(tags) = @tags = tags
  def initialize_copy(source)
    super
    @tags = source.instance_variable_get(:@tags).dup   # deep-ish copy
  end
end
```

---

## 15.8 Introspection And Open Classes

```ruby
rex.class                    #=> Dog
rex.is_a?(Dog)               #=> true
rex.respond_to?(:speak)      #=> true
rex.instance_variables       #=> [:@name, :@age]
rex.instance_variable_get(:@name)
Dog.instance_methods(false)  #=> [:speak, :birthday!, :to_s]
Dog.name                     #=> "Dog"
Dog.superclass               #=> Object
rex.frozen?
rex.object_id
```

Classes are **open**: you can reopen any class and add or change methods, including built-ins. This is powerful (called monkey patching) but dangerous.

```ruby
class String
  def shout = upcase + "!"
end
"hi".shout   #=> "HI!"
```

Prefer your own subclasses, modules, or *refinements* (Lesson 21) to modifying core classes globally.

Namespacing with nested classes and modules:

```ruby
module Shop
  class Cart
    class Item; end
  end
end
Shop::Cart::Item.new
```

---

[Previous](./[14]-Blocks-Procs-And-Lambdas.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[16]-Inheritance-And-Modules.md)
