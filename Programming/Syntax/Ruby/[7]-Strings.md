[Previous](./[6]-Numbers.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[8]-Symbols.md)

*Core Syntax*

# Lesson 7 - Strings (interpolation, heredocs, formatting, encodings)

A `String` is a sequence of characters. Ruby strings are **mutable** (changeable) and have a very rich set of methods.

---

## 7.1 Single vs Double Quotes

```ruby
single = 'Hello, World!'
double = "Hello, World!"
```

The difference is in how special characters are handled:

| | Single quotes `'...'` | Double quotes `"..."` |
|---|---|---|
| Interpolation `#{}` | No | **Yes** |
| Escape sequences (`\n`, `\t`) | Only `\\` and `\'` | **All** |

```ruby
name = "Ada"
puts 'Hi #{name}\n'     # Hi #{name}\n   (printed literally)
puts "Hi #{name}\n"     # Hi Ada  (and a newline)
```

Common escape sequences in double-quoted strings:

| Sequence | Meaning |
|----------|---------|
| `\n` | Newline |
| `\t` | Tab |
| `\\` | Backslash |
| `\"` | Double quote |
| `\e` | Escape character (used for terminal colors) |
| `\u00e9` or `\u{1F600}` | Unicode character |
| `\s` | Space |

### Alternative literals with `%`

Useful when a string contains many quotes:

```ruby
%q(single-quoted style, with 'quotes' and "quotes")   # no interpolation
%Q(double-quoted style, with #{1 + 1})                # interpolation
%(same as %Q)
%w[apple banana cherry]    # array of words (Lesson 14)
```

### Joining strings

Adjacent literals are joined, and `+` and `<<` work too:

```ruby
"Hello, " "World"           # "Hello, World"
"Hello, " + "World"         # "Hello, World"  (new string)
s = "Hello"
s << ", World"              # modifies s in place
"ab" * 3                    # "ababab"
```

`+` only joins strings. `"Age: " + 5` raises `TypeError`, so use interpolation or `to_s`.

---

## 7.2 String Interpolation

Inside a double-quoted string, `#{...}` evaluates **any Ruby expression** and inserts the result, calling `to_s` on it:

```ruby
name = "Ada"
age = 36
puts "#{name} is #{age} years old."          # Ada is 36 years old.
puts "Next year: #{age + 1}"                 # Next year: 37
puts "Shout: #{name.upcase}!"                # Shout: ADA!
puts "Status: #{age >= 18 ? 'adult' : 'minor'}"
puts "List: #{[1, 2, 3].join(', ')}"
```

Interpolation also works in heredocs, symbols (`:"id_#{n}"`), and regular expressions. For instance variables, there is a shortcut: `"#@name"`, though `"#{@name}"` is clearer.

It is generally nicer and faster than building strings with `+`:

```ruby
"Hello, " + name + "! You are " + age.to_s + "."   # clumsy
"Hello, #{name}! You are #{age}."                  # clear
```

To print a literal `#{`, escape the `#`:

```ruby
puts "Use \#{name} to interpolate"   # Use #{name} to interpolate
```

---

## 7.3 Heredocs

A **heredoc** writes a multi-line string without escaping or concatenation.

```ruby
text = <<~HEREDOC
  Dear #{name},

  Thank you for joining!
    This line keeps its extra indent.

  Regards
HEREDOC
puts text
```

Forms:

| Form | Behavior |
|------|----------|
| `<<~ID` | **Squiggly**: removes the common leading indentation. Most useful. Interpolates |
| `<<-ID` | The closing `ID` may be indented, but content keeps its indentation |
| `<<ID` | The closing `ID` must be at the start of the line |
| `<<~'ID'` | Single-quoted ID: **no** interpolation or escapes (raw text) |
| `<<~"ID"` | Explicit double-quoted: interpolates (same as no quotes) |

```ruby
raw = <<~'TEXT'
  No #{interpolation} here and \n stays as typed.
TEXT
puts raw
```

You can call methods on the heredoc opening, and even use several in one line:

```ruby
shout = <<~TEXT.upcase
  quiet words
TEXT
puts shout                          # QUIET WORDS

def show(a, b) = puts(a + b)
show(<<~ONE, <<~TWO)
  first
ONE
  second
TWO
```

SQL, HTML, and help text are typical uses. The identifier (`HEREDOC`, `SQL`, `TEXT`) can be any word, written in uppercase by convention.

---

## 7.4 Common String Methods

```ruby
s = "Hello, Ruby"
```

### Information

```ruby
s.length            # 11  (also size)
s.empty?            # false
"".empty?           # true
s.include?("Ruby")  # true
s.start_with?("He") # true
s.end_with?("by")   # true
s.index("o")        # 4   (position of first match, or nil)
s.rindex("o")       # 4
s.count("l")        # 2   how many 'l' characters
s.casecmp?("HELLO, RUBY")   # true  (case-insensitive compare)
```

### Case

```ruby
s.upcase            # "HELLO, RUBY"
s.downcase          # "hello, ruby"
s.capitalize        # "Hello, ruby"
s.swapcase          # "hELLO, rUBY"
```

### Accessing parts (0-based index)

```ruby
s[0]                # "H"
s[-1]               # "y"   negative counts from the end
s[0, 5]             # "Hello"   start and length
s[0..4]             # "Hello"   range
s[7..]              # "Ruby"    to the end
s["Ruby"]           # "Ruby"    (nil if not found)
s[/R\w+/]           # "Ruby"    by regular expression
s.slice(0, 5)       # "Hello"
s.chars             # ["H", "e", "l", ...]
s.bytes             # byte values
s.lines             # array of lines
```

### Cleaning

```ruby
"  padded  ".strip      # "padded"   (lstrip, rstrip for one side)
"line\n".chomp          # "line"     remove trailing newline
"hello".chomp("lo")     # "hel"      remove a given ending
"hello".chop            # "hell"     remove the last character
"aaabbb".squeeze        # "ab"       collapse repeats
"hello".delete("l")     # "heo"
```

### Changing

```ruby
s.reverse               # "ybuR ,olleH"
s.sub("l", "L")         # "HeLlo, Ruby"   first match only
s.gsub("l", "L")        # "HeLLo, RubY"   all matches (global)
s.tr("lo", "01")        # "He001, Ruby"   character-for-character
s.replace("new")        # change the string in place
s.insert(5, "!")        # insert at an index (mutates)
s.prepend(">> ")        # add to the front (mutates)
```

### Splitting and joining

```ruby
"a,b,c".split(",")          # ["a", "b", "c"]
"a b  c".split              # ["a", "b", "c"]  (splits on whitespace)
"abc".split("")             # ["a", "b", "c"]
"a,b,c".split(",", 2)       # ["a", "b,c"]   limit
%w[a b c].join("-")         # "a-b-c"
```

### Padding and alignment

```ruby
"Ruby".center(10, "*")      # "***Ruby***"
"Ruby".ljust(8, ".")        # "Ruby...."
"Ruby".rjust(8)             # "    Ruby"
```

### Conversion and comparison

```ruby
"42".to_i            # 42
"3.5".to_f           # 3.5
"abc".to_sym         # :abc
"a".ord              # 97      (character to number)
97.chr               # "a"
"a" <=> "b"          # -1
"a" == "a"           # true
"b".between?("a", "c")  # true
"ruby" * 2           # "rubyruby"
"Ruby".succ          # "Rubz"
```

### Iterating

```ruby
"abc".each_char { |c| puts c }
"a\nb".each_line { |line| puts line }
"abc".each_char.with_index { |c, i| puts "#{i}: #{c}" }
```

### Methods ending in `!`

Many methods have a **bang** version that changes the string in place and returns `nil` if nothing changed:

```ruby
str = "hello"
str.upcase!      # "HELLO"  and str is now "HELLO"
str.upcase!      # nil      (nothing changed this time)
```

---

## 7.5 Formatting with format and printf

`format` (also `sprintf`) builds a string from a **template** with `%` placeholders. `printf` does the same but prints it. The `%` operator on a string is a shortcut.

```ruby
format("%d items", 5)                 # "5 items"
format("%.2f", 3.14159)               # "3.14"
format("%s is %d", "Ada", 36)         # "Ada is 36"
printf("%-10s|%5d|\n", "Ada", 36)     # Ada       |   36|
"%.1f%%" % 12.345                      # "12.3%"
"%s and %s" % ["a", "b"]               # "a and b"
```

### Common placeholders

| Placeholder | Meaning | Example |
|-------------|---------|---------|
| `%d` | Integer | `format("%d", 42)` gives `42` |
| `%f` | Float | `format("%.3f", 2)` gives `2.000` |
| `%s` | String (uses `to_s`) | `format("%s", :a)` gives `a` |
| `%p` | `inspect` form | `format("%p", "a")` gives `"a"` |
| `%x` / `%X` | Hexadecimal | `format("%x", 255)` gives `ff` |
| `%o` | Octal | `format("%o", 8)` gives `10` |
| `%b` | Binary | `format("%b", 5)` gives `101` |
| `%e` | Scientific notation | `format("%e", 12345.6)` gives `1.234560e+04` |
| `%c` | Character | `format("%c", 65)` gives `A` |
| `%%` | A literal percent sign | |

### Flags and width

| Flag | Effect | Example |
|------|--------|---------|
| number | Minimum width | `%5d` gives `   42` |
| `-` | Left-align | `%-5d` gives `42   ` |
| `0` | Pad with zeros | `%05d` gives `00042` |
| `+` | Always show the sign | `%+d` gives `+42` |
| space | Space for positive sign | `% d` gives ` 42` |
| `.N` | Precision (decimals, or max characters for strings) | `%.2f`, `%.3s` |
| `,` | Not supported; see below | |

### Named references

```ruby
format("%<name>s is %<age>d", name: "Ada", age: 36)   # "Ada is 36"   (type specified)
format("%{name} is %{age}", name: "Ada", age: 36)     # "Ada is 36"   (no type, uses to_s)
```

### A tidy table

```ruby
rows = [["Apple", 1.5, 3], ["Banana", 0.25, 12]]
rows.each { |n, price, qty| puts format("%-10s $%6.2f x %3d", n, price, qty) }
# Apple      $  1.50 x   3
# Banana     $  0.25 x  12
```

Ruby does not add thousands separators on its own. Use a regex trick or a gem/Rails helper:

```ruby
1234567.to_s.gsub(/(\d)(?=(\d{3})+$)/, '\1,')   # "1,234,567"
```

---

## 7.6 Mutability and Frozen String Literals

Strings are **mutable**, so a method can change a string that other variables also refer to:

```ruby
a = "hi"
b = a
b << "!"
puts a          # hi!    (same object)
a.equal?(b)     # true   (same identity)
```

Make a copy when you need independence:

```ruby
c = a.dup
c << "?"
puts a          # hi!
```

### Frozen strings

A **frozen** object cannot be modified:

```ruby
s = "ruby".freeze
s.frozen?            # true
# s << "!"           # FrozenError: can't modify frozen String
t = s.dup            # a dup is not frozen
t << "!"             # OK
```

### The magic comment

Putting this at the top of a file freezes every string literal in it:

```ruby
# frozen_string_literal: true

greeting = "hello"
# greeting << "!"    # FrozenError
greeting += "!"      # OK: this creates a NEW string and reassigns
```

Benefits: it prevents accidental mutation, and identical literals can share one object, which saves memory. It is the style used by most modern Ruby code and enforced by RuboCop by default.

### "Chilled" strings in recent Ruby versions

Without the magic comment, string literals are **chilled** in recent Ruby versions. They behave as mutable, but modifying one can emit a **deprecation warning** (shown when you run with `-W:deprecated` or `-w`), because a future Ruby is planned to freeze them by default. Writing `# frozen_string_literal: true` and using `+""`, `.dup`, or `String.new` when you need a mutable string avoids surprises.

```ruby
+"text"        # unary plus: an unfrozen copy (or itself if not frozen)
-"text"        # unary minus: a frozen, de-duplicated string
String.new("text")   # always a new mutable string
"text".dup           # a mutable copy
```

Interpolated strings (`"a#{1}"`) are **not** frozen by the magic comment in current Ruby versions.

---

## 7.7 Encodings and Unicode

Every string has an **encoding** that says how characters map to bytes. Ruby source files and strings default to **UTF-8**, which covers virtually all human languages and emoji.

```ruby
s = "héllo 😀"
puts s.encoding           # UTF-8
puts s.length             # 7    characters
puts s.bytesize           # 11   bytes
puts s.bytes.inspect      # [104, 195, 169, 108, 108, 111, 32, 240, 159, 152, 128]
puts s.reverse            # 😀 olléh   (works on characters, not bytes)
puts s.upcase             # HÉLLO 😀
puts "\u00e9"             # é
puts "\u{1F600}"          # 😀
puts "é".ord              # 233   Unicode code point
puts 233.chr(Encoding::UTF_8)   # é
```

### Converting encodings

```ruby
latin = "café".encode("ISO-8859-1")
puts latin.encoding              # ISO-8859-1
puts latin.bytesize              # 4
utf = latin.encode("UTF-8")      # back to UTF-8
```

Characters that cannot be converted raise errors unless you say what to do:

```ruby
"日本".encode("ASCII", invalid: :replace, undef: :replace, replace: "?")   # "??"
```

### Invalid byte sequences

```ruby
bad = "abc\xFF"
bad.valid_encoding?      # false
bad.scrub("?")           # "abc?"   replace invalid bytes
```

### `force_encoding` vs `encode`

- `encode` **converts** the characters to another encoding (the bytes change).
- `force_encoding` only **relabels** the same bytes (use it when you know the data was tagged wrongly, for example bytes read from a file or network).

```ruby
data = "caf\xC3\xA9".b                  # binary string (ASCII-8BIT / BINARY)
data.force_encoding("UTF-8")            # now "café"
puts data
```

### Normalization

The same visible character can be built in different ways ("é" as one code point, or "e" plus a combining accent). Normalize before comparing:

```ruby
a = "\u00e9"             # é (one code point)
b = "e\u0301"            # é (two code points)
puts a == b                                   # false
puts a.unicode_normalize == b.unicode_normalize   # true
```

(`unicode_normalize` may load the Unicode tables the first time it is used.)

### Good habits

- Keep everything **UTF-8** inside your program, and convert at the edges when reading and writing legacy data.
- Use `length` for characters and `bytesize` when a limit is in bytes.
- Open files with an explicit encoding when needed: `File.read("x.txt", encoding: "UTF-8")`.
- `"\xFF"` style bytes and `.b` produce **binary** strings (`ASCII-8BIT`, also named `BINARY`).

More text-processing tools appear in Lesson 58.

---

[Previous](./[6]-Numbers.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[8]-Symbols.md)