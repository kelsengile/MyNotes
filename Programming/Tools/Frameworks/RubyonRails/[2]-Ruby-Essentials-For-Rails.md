[Previous](./[1]-What-Is-Rails.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[3]-Gems-Bundler-And-Generators.md)

*Basics*

# Lesson 2 - Ruby Essentials For Rails

## 2.1 Objects and blocks

In Ruby, **everything is an object**, including numbers, strings, and `nil`. You call methods on objects with a dot:

```ruby
5.class            # => Integer
"rails".upcase     # => "RAILS"
nil.to_a           # => []
[3, 1, 2].sort     # => [1, 2, 3]
```

Parentheses on method calls are optional, which is why Rails code reads like a sentence: `puts "hi"`, `validates :title, presence: true`.

**Blocks** are chunks of code passed to a method, written with `do ... end` (multi-line) or `{ ... }` (one line):

```ruby
[1, 2, 3].each do |n|
  puts n * 2
end

squares = [1, 2, 3].map { |n| n * n }   # => [1, 4, 9]
```

Rails uses blocks everywhere: routes, migrations, and forms.

```ruby
resources :articles do
  resources :comments
end
```

You can write your own method that accepts a block using `yield`:

```ruby
def with_timing
  start = Time.now
  result = yield
  puts "Took #{Time.now - start}s"
  result
end

with_timing { sleep 0.1 }
```

A block can be stored as an object, called a **proc** or **lambda**. Lambdas are used in Rails for scopes: `-> { where(published: true) }`.

---

## 2.2 Symbols and hashes

A **symbol** is a lightweight, immutable name written with a leading colon: `:title`. Symbols are used as identifiers, such as hash keys, option names, and association names.

A **hash** stores key–value pairs. The modern syntax uses `key: value`, which creates symbol keys:

```ruby
article = { title: "Hello", published: true }
article[:title]          # => "Hello"
article[:views] = 10     # add a key
article.fetch(:missing, 0)  # => 0 (default if key is absent)
```

Methods can take **keyword arguments**, and braces are optional when a hash is the last argument:

```ruby
def greet(name:, punctuation: "!")
  "Hello, #{name}#{punctuation}"
end

greet(name: "Ada")                    # => "Hello, Ada!"
greet(name: "Ada", punctuation: ".")  # => "Hello, Ada."
```

This is why Rails calls look like this:

```ruby
validates :title, presence: true, length: { minimum: 5 }
```

That is `validates(:title, { presence: true, length: { minimum: 5 } })`.

Useful hash tools: `keys`, `values`, `merge`, `slice`, `each`, and `dig` for nested access: `data.dig(:user, :address, :city)`.

---

## 2.3 Modules and mixins

A **module** is a named bundle of methods that is not meant to be instantiated. It has two main jobs.

**1. Namespacing**: grouping related classes so names don't clash:

```ruby
module Admin
  class ArticlesController
  end
end
# referred to as Admin::ArticlesController
```

**2. Mixins**: sharing behavior between classes. `include` adds the module's methods as *instance* methods, and `extend` adds them as *class* methods:

```ruby
module Greeting
  def greet
    "Hello from #{self.class.name}"
  end
end

class User
  include Greeting
end

User.new.greet   # => "Hello from User"
```

Ruby classes have single inheritance (one parent), but a class can include many modules.

Rails relies on this heavily. `ApplicationRecord` inherits from `ActiveRecord::Base`, and `ActiveSupport::Concern` is a helper for writing reusable modules you put in `app/models/concerns` or `app/controllers/concerns`:

```ruby
# app/models/concerns/sluggable.rb
module Sluggable
  extend ActiveSupport::Concern

  included do
    before_validation :generate_slug
  end

  private

  def generate_slug
    self.slug ||= title.to_s.parameterize
  end
end

class Article < ApplicationRecord
  include Sluggable
end
```

---

## 2.4 Metaprogramming glimpses

Metaprogramming means code that writes or changes code at runtime. You don't need to master it, but recognizing it explains much of Rails' "magic".

**Class-level macros.** `attr_accessor` is just a method call that defines methods:

```ruby
class Person
  attr_accessor :name   # defines name and name=
end
```

`has_many :comments` and `validates :title, presence: true` work the same way: they are class methods that define behavior on your model.

**`send` and `public_send`** call a method by name:

```ruby
"hello".public_send(:upcase)   # => "HELLO"
article.public_send("title")   # same as article.title
```

**`define_method`** creates methods dynamically:

```ruby
class Status
  %w[draft published archived].each do |name|
    define_method("#{name}?") { @value == name }
  end

  def initialize(value) = @value = value
end

Status.new("draft").draft?      # => true
Status.new("draft").archived?   # => false
```

**`method_missing`** runs when an undefined method is called. Active Record's old `find_by_title` finders worked this way. If you use it, also define `respond_to_missing?`:

```ruby
class Settings
  def initialize(data) = @data = data

  def method_missing(name, *args)
    @data.key?(name) ? @data[name] : super
  end

  def respond_to_missing?(name, include_private = false)
    @data.key?(name) || super
  end
end

Settings.new(theme: "dark").theme   # => "dark"
```

Tip: when a Rails method seems magical, ask "which class method or module is defining this?" and use `object.method(:name).source_location` in the console to find it.

> Official reference: [Ruby documentation](https://www.ruby-lang.org/en/documentation/)

---

[Previous](./[1]-What-Is-Rails.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[3]-Gems-Bundler-And-Generators.md)