[Previous](./[14]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[16]-Ranges-and-Sets.md)

*Core Syntax*

# Lesson 15 - Hashes

A **Hash** stores **key-value pairs**, like a dictionary or a map in other languages. You look values up by key instead of by position. Hashes remember the **order in which keys were inserted**.

---

## 15.1 Creating Hashes

### Literals

```ruby
# Symbol keys (most common)
person = { name: "Ada", age: 36 }

# Any object as a key, using "hash rockets" =>
scores = { "alice" => 90, "bob" => 85 }
mixed = { 1 => "one", :two => 2, "three" => 3.0 }

empty = {}
```

`{ name: "Ada" }` is exactly `{ :name => "Ada" }`.

### Hash.new

```ruby
Hash.new                      # {}
Hash.new(0)                   # {} where missing keys read as 0
Hash.new { |hash, key| hash[key] = [] }   # compute defaults with a block
```

### From other data

```ruby
[[:a, 1], [:b, 2]].to_h                 # {a: 1, b: 2}
%w[a b c].zip([1, 2, 3]).to_h           # {"a"=>1, "b"=>2, "c"=>3}
%w[apple kiwi].to_h { |w| [w, w.length] }   # {"apple"=>5, "kiwi"=>4}
Hash[[[:x, 1], [:y, 2]]]                # {x: 1, y: 2}
{ a: 1 }.to_a                           # [[:a, 1]]
```

Keys must be **unique**. A repeated key keeps the last value. Keys are normally immutable objects (symbols, strings, numbers). A string key is copied and frozen automatically.

---

## 15.2 Reading and Writing Keys

### Reading

```ruby
person = { name: "Ada", age: 36, city: nil }

person[:name]            # "Ada"
person[:missing]         # nil
person.fetch(:name)      # "Ada"
person.fetch(:zip, "n/a")           # "n/a"       (default)
person.fetch(:zip) { |k| "no #{k}" } # "no zip"    (block)
# person.fetch(:zip)                # KeyError
person.dig(:name)        # "Ada"
person.values_at(:name, :age)       # ["Ada", 36]
person.fetch_values(:name, :age)    # ["Ada", 36]  (KeyError if missing)
```

`[]` returns `nil` both for a missing key *and* for a key whose value is `nil`. To tell them apart:

```ruby
person.key?(:city)       # true   (also has_key?, include?, member?)
person.key?(:zip)        # false
person.value?(36)        # true   (also has_value?)
person.key(36)           # :age   (first key with that value)
```

### Information

```ruby
person.keys        # [:name, :age, :city]
person.values      # ["Ada", 36, nil]
person.length      # 3  (also size, count)
person.empty?      # false
person.first       # [:name, "Ada"]
```

### Writing

```ruby
person[:email] = "ada@example.com"     # add a key
person[:age] = 37                      # update a key
person.store(:job, "Engineer")         # same as []=
person.delete(:city)                   # removes the key, returns its value
person.delete(:nope) { |k| "no #{k}" } # block runs when the key is missing
person.delete_if { |k, v| v.nil? }     # removes pairs, in place
person.clear                           # empties the hash
```

### Equality

Two hashes are equal if they have the same pairs, regardless of order:

```ruby
{ a: 1, b: 2 } == { b: 2, a: 1 }   # true
```

---

## 15.3 Default Values

By default, reading a missing key gives `nil`. A **default** changes that.

```ruby
counts = Hash.new(0)
counts[:apples] += 1      # counts[:apples] is 0 first, then becomes 1
counts[:apples] += 1
p counts                  # {apples: 2}
p counts[:pears]          # 0   (but pears is NOT stored)
```

Classic use: counting words.

```ruby
text = "the cat and the hat and the bat"
freq = Hash.new(0)
text.split.each { |word| freq[word] += 1 }
p freq     # {"the"=>3, "cat"=>1, "and"=>2, "hat"=>1, "bat"=>1}
# shorter: text.split.tally
```

### The shared default trap

A default **object** is shared by every missing key. For mutable defaults (arrays, hashes), that is a bug:

```ruby
bad = Hash.new([])
bad[:a] << 1
bad[:b] << 2
p bad          # {}   nothing was stored!
p bad[:z]      # [1, 2]  the one shared default array grew

good = Hash.new { |hash, key| hash[key] = [] }   # a fresh array per key, stored
good[:a] << 1
good[:b] << 2
p good         # {a: [1], b: [2]}
```

### Other default tools

```ruby
h = { a: 1 }
h.default = 0          # set the default after creation
h.default_proc = proc { |hash, key| key.to_s }
h.fetch(:a)            # fetch ignores defaults and raises/uses its own
h.dig(:a)
```

Grouping with a default block:

```ruby
by_length = Hash.new { |h, k| h[k] = [] }
%w[a bb cc d].each { |w| by_length[w.length] << w }
p by_length       # {1=>["a", "d"], 2=>["bb", "cc"]}
```

---

## 15.4 Iterating Over Hashes

Iteration follows **insertion order**.

```ruby
prices = { apple: 1.5, banana: 0.25, cherry: 4.0 }

prices.each { |key, value| puts "#{key}: #{value}" }
prices.each_pair { |key, value| puts "#{key}: #{value}" }   # same
prices.each_key { |key| puts key }
prices.each_value { |value| puts value }
prices.each_with_index { |(key, value), i| puts "#{i} #{key} #{value}" }
prices.each.with_index(1) { |(k, v), i| puts "#{i}. #{k}" }
```

Notice the parentheses `(key, value)` when another parameter such as the index comes along.

Without destructuring, the block receives a two-element array:

```ruby
prices.each { |pair| p pair }      # [:apple, 1.5]
```

### Sorting and ordering

```ruby
prices.sort_by { |name, price| price }        # array of pairs, cheapest first
prices.sort_by { |_, price| -price }.to_h     # back to a hash, most expensive first
prices.min_by { |_, price| price }            # [:banana, 0.25]
prices.max_by { |_, price| price }.first      # :cherry
prices.sum { |_, price| price }               # 5.75
prices.count { |_, price| price > 1 }         # 2
prices.find { |_, price| price > 1 }          # [:apple, 1.5]
prices.any? { |_, price| price > 3 }          # true
prices.all? { |_, price| price > 0 }          # true
```

> Do not add or remove keys while iterating a hash. Ruby raises an error for adding keys during iteration.

---

## 15.5 Transforming and Merging Hashes

### Filtering

```ruby
prices.select { |_, p| p > 1 }       # {apple: 1.5, cherry: 4.0}   (also filter)
prices.reject { |_, p| p > 1 }       # {banana: 0.25}
prices.slice(:apple, :cherry)        # only those keys
prices.except(:banana)               # all but these keys (Ruby 3.0+)
prices.compact                       # drops pairs whose value is nil
prices.filter_map { |k, v| k if v > 1 }   # [:apple, :cherry] (returns an array)
```

### Transforming keys and values

```ruby
prices.transform_values { |p| (p * 100).to_i }     # {apple: 150, banana: 25, cherry: 400}
prices.transform_keys(&:to_s)                      # {"apple"=>1.5, ...}
prices.transform_keys(apple: :fruit)               # rename specific keys
prices.map { |k, v| [k.to_s, v * 2] }.to_h         # build a new hash through arrays
prices.to_h { |k, v| [k.to_s.upcase, v] }          # same idea, shorter
prices.invert                                      # {1.5=>:apple, 0.25=>:banana, ...}
prices.group_by { |_, p| p > 1 }                   # {true=>[[:apple, 1.5], ...], false=>[...]}
prices.partition { |_, p| p > 1 }                  # two arrays of pairs
```

Most methods that take a block return a **new** hash. Versions ending in `!` (`select!`, `reject!`, `transform_values!`, `merge!`) change the original.

### Merging

```ruby
a = { x: 1, y: 2 }
b = { y: 20, z: 30 }

a.merge(b)                     # {x: 1, y: 20, z: 30}   (b wins on conflicts)
a.merge(b) { |key, old, new| old + new }   # {x: 1, y: 22, z: 30}   (resolve conflicts)
a.merge!(b)                    # changes a   (also update)
{ **a, **b }                   # merge with double splat
a.merge(b, { w: 0 })           # several hashes at once
```

### Counting, summing, grouping

```ruby
words = %w[apple avocado banana blueberry cherry]
words.group_by { |w| w[0] }            # {"a"=>["apple", "avocado"], "b"=>[...], "c"=>[...]}
words.tally_by { |w| w.length }        # {5=>1, 7=>1, 6=>2, 9=>1}  (Ruby 3.1+)
words.each_with_object(Hash.new(0)) { |w, h| h[w[0]] += 1 }
```

### Converting

```ruby
prices.to_a              # [[:apple, 1.5], ...]
prices.keys.zip(prices.values)
prices.sort.to_h         # sorted by key
prices.min_by(2) { |_, p| p }.to_h
```

---

## 15.6 Nested Hashes

Hashes can hold other hashes and arrays, which is how JSON-like data is represented.

```ruby
company = {
  name: "Acme",
  address: { city: "London", zip: "N1" },
  employees: [
    { name: "Ada", skills: %w[math code] },
    { name: "Alan", skills: %w[logic] }
  ]
}

company[:address][:city]                  # "London"
company[:employees][0][:name]             # "Ada"
company[:employees].map { |e| e[:name] }  # ["Ada", "Alan"]
```

### dig: safe deep access

```ruby
company.dig(:address, :city)                 # "London"
company.dig(:employees, 0, :skills, 1)       # "code"
company.dig(:address, :country, :name)       # nil  (no error)
company[:address][:country][:name]           # NoMethodError on nil
```

### Updating deep data

```ruby
company[:address][:zip] = "N2"
company[:employees] << { name: "Grace", skills: [] }
company[:address].delete(:zip)
```

### Auto-creating nested levels

```ruby
tree = Hash.new { |h, k| h[k] = Hash.new(&h.default_proc) }
tree[:a][:b][:c] = 1
p tree       # {a: {b: {c: 1}}}
```

### Copies

`dup` is shallow: inner hashes are shared. Use a deep copy when you need independence:

```ruby
copy = Marshal.load(Marshal.dump(company))
```

### JSON

```ruby
require "json"
json = JSON.generate(company)                 # hash to JSON text
parsed = JSON.parse(json, symbolize_names: true)   # JSON text back to a hash
puts JSON.pretty_generate(company)
```

---

## 15.7 Hash Shorthand Syntax

Ruby has several shortcuts for writing hashes.

### Symbol keys with a colon

```ruby
{ name: "Ada" }          # same as { :name => "Ada" }
{ "first name": "Ada" }  # symbol key :"first name" (quotes allowed)
```

### Value omission (Ruby 3.1+)

When the key and a local variable/method have the same name, leave out the value:

```ruby
name = "Ada"
age = 36
person = { name:, age: }      # { name: name, age: age }
```

### Braces are optional in method calls

```ruby
def create(options)
  p options
end

create(color: "red", size: 5)      # the braces are implied
puts "Hello", sep: "-"             # trailing hash becomes an argument (or keywords)
```

### Double splat `**`

```ruby
defaults = { color: "red", size: 5 }
custom = { size: 10 }
settings = { **defaults, **custom }    # {color: "red", size: 10}
def draw(color:, size:) = "#{color} #{size}"
draw(**settings)
```

### Hash with `=>` mixing types

```ruby
mixed = { name: "Ada", "title" => "Countess", 1 => :one }
```

### Pattern matching and destructuring

```ruby
person = { name: "Ada", age: 36 }
person => { name: }        # rightward pattern match (Lesson 19)
puts name                  # Ada
```

### Block with a hash returned from `map`

```ruby
users = %w[ada alan]
users.to_h { |u| [u, u.capitalize] }        # {"ada"=>"Ada", "alan"=>"Alan"}
users.each_with_object({}) { |u, h| h[u] = u.length }
```

### Key order and comparison

Hashes keep insertion order, and `compare_by_identity` makes a hash use object identity instead of `eql?` for keys (rarely needed):

```ruby
h = {}.compare_by_identity
h["a"] = 1
h["a".dup] = 2       # two different keys
```

---

[Previous](./[14]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[16]-Ranges-and-Sets.md)