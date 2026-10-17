[Previous](./[10]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[12]-Enumerable-And-Iterators.md)

*Collections*

# Lesson 11 - Hashes

## 11.1 Creating Hashes

A `Hash` stores key-value pairs and remembers **insertion order**.

```ruby
person = { name: "Ada", age: 36, languages: ["Ruby", "C"] }   # symbol keys
scores = { "alice" => 90, "bob" => 85 }                       # string keys
mixed = { 1 => "one", :two => 2, [3] => "array key" }
empty = {}
Hash.new
```

`name: "Ada"` is shorthand for `:name => "Ada"`. Quote keys that contain spaces: `{ "full name": "Ada" }` creates a symbol key `:"full name"`.

Value omission shorthand (Ruby 3.1+):

```ruby
x = 1
y = 2
point = { x:, y: }      #=> {x: 1, y: 2}
```

---

## 11.2 Reading Values

```ruby
person = { name: "Ada", age: 36 }

person[:name]            #=> "Ada"
person[:missing]         #=> nil
person.fetch(:name)      #=> "Ada"
person.fetch(:missing)   # KeyError
person.fetch(:missing, "n/a")   #=> "n/a"
person.fetch(:missing) { |k| "no #{k}" }
person.dig(:name)
{ a: { b: { c: 1 } } }.dig(:a, :b, :c)   #=> 1
person.key?(:age)        #=> true  (aliases: has_key?, include?, member?)
person.value?(36)        #=> true
person.key(36)           #=> :age (first key with that value)
person.keys              #=> [:name, :age]
person.values            #=> ["Ada", 36]
person.values_at(:name, :age)
person.size              #=> 2
person.empty?            #=> false
person.first             #=> [:name, "Ada"]
```

---

## 11.3 Adding, Updating, And Removing

```ruby
h = { a: 1 }

h[:b] = 2                    # add or update
h.store(:c, 3)
h.merge({ d: 4 })            # returns a new hash
h.merge!(e: 5)               # modifies h (alias: update)
h.merge({ a: 10 }) { |key, old, new| old + new }   # resolve conflicts
h.delete(:a)                 # returns the value
h.delete_if { |k, v| v > 3 }
h.reject! { |k, v| v.nil? }
h.compact                    # remove nil values
h.slice(:b, :c)              # only these keys
h.except(:b)                 # all but these keys
h.clear
```

Merging with symbols vs strings matters: `{ "a" => 1 }` and `{ a: 1 }` are different keys.

---

## 11.4 Iterating And Transforming

```ruby
prices = { apple: 1.2, banana: 0.5, cherry: 3.0 }

prices.each { |item, price| puts "#{item}: #{price}" }
prices.each_pair { |k, v| }
prices.each_key { |k| }
prices.each_value { |v| }
prices.each_with_index { |(k, v), i| puts "#{i} #{k}" }

prices.map { |k, v| [k, v * 2] }.to_h
prices.to_h { |k, v| [k.to_s, v] }
prices.transform_values { |v| (v * 100).to_i }
prices.transform_keys(&:to_s)
prices.select { |k, v| v > 1 }       #=> {apple: 1.2, cherry: 3.0}
prices.filter_map { |k, v| k if v > 1 }   #=> [:apple, :cherry]
prices.reject { |k, v| v > 1 }
prices.find { |k, v| v > 2 }         #=> [:cherry, 3.0]
prices.sort_by { |k, v| v }          #=> [[:banana, 0.5], [:apple, 1.2], ...]
prices.min_by { |k, v| v }           #=> [:banana, 0.5]
prices.sum { |k, v| v }              #=> 4.7
prices.count { |k, v| v > 1 }
prices.any? { |k, v| v > 2 }
prices.partition { |k, v| v > 1 }
prices.group_by { |k, v| v > 1 }
prices.invert                        #=> {1.2 => :apple, ...}
prices.each_with_object({}) { |(k, v), acc| acc[v] = k }
```

`sort_by` returns an array of pairs; call `.to_h` to turn it back into a hash.

---

## 11.5 Default Values

```ruby
counts = Hash.new(0)
"hello".each_char { |c| counts[c] += 1 }
counts            #=> {"h" => 1, "e" => 1, "l" => 2, "o" => 1}

groups = Hash.new { |hash, key| hash[key] = [] }
groups[:fruits] << "apple"
groups[:fruits] << "pear"
groups            #=> {fruits: ["apple", "pear"]}
```

**Gotcha:** `Hash.new([])` shares one array among all missing keys and never stores it. Use the block form instead. Reading a missing key with a default does not add the key; `default=` and `default_proc` can be changed later.

Quick counting with `tally`:

```ruby
%w[a b a c a].tally   #=> {"a" => 3, "b" => 1, "c" => 1}
```

---

## 11.6 Hash Details

- **Order**: keys keep insertion order.
- **Keys**: any object works, but it must have consistent `hash` and `eql?`. Strings used as keys are duplicated and frozen automatically. Do not mutate an object after using it as a key.
- **Equality**: two hashes are `==` if they have the same pairs (order does not matter).
- **Nested structures**: use `dig` and `fetch` to avoid `nil` errors.
- **Converting**: `array.to_h`, `hash.to_a`, `hash.keys`, `Hash[[[:a, 1]]]`.
- **Identity keys**: `h.compare_by_identity` compares keys by object identity.
- **Frozen**: `h.freeze` prevents changes.
- **Printing**: Ruby 3.4 changed `inspect` to `{name: "Ada", "key" => 1}` (symbol keys without `=>` spacing, others with spaces); earlier versions printed `{:name=>"Ada"}`.

```ruby
a = { x: 1, y: 2 }
b = { y: 2, x: 1 }
a == b          #=> true
a.to_a          #=> [[:x, 1], [:y, 2]]
Hash[[[:k, :v]]]#=> {k: :v}
```

Keyword arguments use hashes under the hood (see Lesson 13):

```ruby
def connect(host:, port: 80, **options)
  puts "#{host}:#{port} #{options}"
end
connect(host: "x.com", ssl: true)
config = { host: "y.com", port: 8080 }
connect(**config)       # double splat spreads a hash into keywords
```

---

## 11.7 Hashes With Pattern Matching

```ruby
user = { name: "Ada", role: :admin, tags: %w[a b] }

case user
in { role: :admin, name: }
  puts "Admin #{name}"
end

user => { tags: [first_tag, *] }
puts first_tag     #=> a
```

---

[Previous](./[10]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[12]-Enumerable-And-Iterators.md)
