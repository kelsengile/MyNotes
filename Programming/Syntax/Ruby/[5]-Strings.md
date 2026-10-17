[Previous](./[4]-Numbers-And-Math.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[6]-Symbols-Ranges-And-Nil.md)

*Basics*

# Lesson 5 - Strings

## 5.1 Creating Strings

```ruby
single = 'No interpolation: #{x}\n'      # only \\ and \' are escapes
double = "Interpolation: #{1 + 1}\n"     # supports all escapes
empty = String.new
generic = %q(single-quoted style, "quotes" are fine)
generic2 = %Q(double-quoted style with #{1 + 2})
```

Common escapes in double-quoted strings: `\n` newline, `\t` tab, `\\` backslash, `\"` quote, `\e` escape, `\u00e9` Unicode character (`é`).

**Heredocs** hold multi-line text:

```ruby
text = <<~TEXT
  Dear #{name},
    This line keeps its extra indent.
  Thank you!
TEXT
```

`<<~` strips the common leading whitespace. Use `<<~'TEXT'` to disable interpolation.

Concatenation:

```ruby
"foo" + "bar"      #=> "foobar"   (creates a new string)
"foo" << "bar"     # appends in place (mutates)
"ab" * 3           #=> "ababab"
"foo" "bar"        #=> "foobar"   (adjacent literals join)
```

---

## 5.2 Common String Methods

```ruby
s = "Hello, Ruby"

s.length           #=> 11
s.upcase           #=> "HELLO, RUBY"
s.downcase         #=> "hello, ruby"
s.capitalize       #=> "Hello, ruby"
s.swapcase         #=> "hELLO, rUBY"
s.reverse          #=> "ybuR ,olleH"
s.include?("Ruby") #=> true
s.start_with?("He")#=> true
s.end_with?("by")  #=> true
s.index("R")       #=> 7
s.count("l")       #=> 2
s.empty?           #=> false
"  pad  ".strip    #=> "pad"
"  pad  ".lstrip   #=> "pad  "
"  pad  ".rstrip   #=> "  pad"
"line\n".chomp     #=> "line"
"hello".chop       #=> "hell"
"a,b,c".split(",") #=> ["a", "b", "c"]
"hello world".split #=> ["hello", "world"]
["a", "b"].join("-")#=> "a-b"
"abc".chars        #=> ["a", "b", "c"]
"a\nb".lines       #=> ["a\n", "b"]
"abc".each_char { |c| print c }
"abc".succ         #=> "abd"
```

---

## 5.3 Indexing And Slicing

```ruby
s = "Ruby rocks"

s[0]          #=> "R"
s[-1]         #=> "s"
s[0, 4]       #=> "Ruby"     (start, length)
s[0..3]       #=> "Ruby"     (range)
s[5..]        #=> "rocks"    (endless range)
s[/r\w+/]     #=> "rocks"    (regex)
s["Ruby"]     #=> "Ruby"     (substring or nil)
s[100]        #=> nil
s.slice(0, 4) #=> "Ruby"
```

Strings can be modified in place through indexes:

```ruby
t = +"Ruby rocks"     # unary + returns an unfrozen copy if needed
t[0] = "J"
t                     #=> "Juby rocks"
```

---

## 5.4 Modifying Strings: Bang Methods

Many methods come in two forms: one that returns a new string, and a `!` version that changes the receiver (returning `nil` if nothing changed).

```ruby
s = +"hello"
s.upcase          #=> "HELLO"   (s is still "hello")
s.upcase!         # s is now "HELLO"
s.capitalize!
s.replace("new")  # replaces content
s.insert(0, ">> ")
s.prepend("# ")
s.concat("!", "?")
s.clear           # ""
```

Replacing text:

```ruby
"hello world".sub("o", "0")     #=> "hell0 world"  (first match)
"hello world".gsub("o", "0")    #=> "hell0 w0rld"  (all matches)
"hello".gsub(/[el]/, "e" => "3", "l" => "1")   #=> "h3110"
"hello".tr("el", "ip")          #=> "hippo"
"aaabbb".squeeze                #=> "ab"
"hello".delete("l")             #=> "heo"
```

---

## 5.5 Formatting Strings

```ruby
format("%d items", 3)               #=> "3 items"
format("%.2f", 3.14159)             #=> "3.14"
format("%05d", 42)                  #=> "00042"
format("%-10s|", "left")            #=> "left      |"
format("%10s|", "right")            #=> "     right|"
format("%x %o %b %e", 255, 8, 5, 12345.678)
"%s is %d years old" % ["Ada", 36]
"%<name>s: %<score>.1f" % { name: "Bo", score: 9.54 }   #=> "Bo: 9.5"

"hi".ljust(6, ".")     #=> "hi...."
"hi".rjust(6, ".")     #=> "....hi"
"hi".center(6, "*")    #=> "**hi**"
1234567.to_s.reverse.scan(/\d{1,3}/).join(",").reverse  #=> "1,234,567"
```

---

## 5.6 Comparing And Converting

```ruby
"a" == "a"             #=> true
"a".eql?("a")          #=> true
"a".equal?("a")        #=> false  (different objects)
"apple" <=> "banana"   #=> -1
"a".casecmp("A")       #=> 0
"a".casecmp?("A")      #=> true

"42".to_i              #=> 42
"3.5".to_f             #=> 3.5
"abc".to_sym           #=> :abc
"abc".to_s             #=> "abc"
42.to_s                #=> "42"
:abc.to_s              #=> "abc"
"Hello".ord            #=> 72
72.chr                 #=> "H"
"hello".hash           # integer used by Hash lookups
```

---

## 5.7 Encodings, Bytes, And Unicode

Ruby strings are sequences of characters with an encoding, UTF-8 by default.

```ruby
s = "café"
s.length                 #=> 4   (characters)
s.bytesize               #=> 5   (bytes)
s.encoding               #=> #<Encoding:UTF-8>
s.bytes                  #=> [99, 97, 102, 195, 169]
s.valid_encoding?        #=> true
s.encode("ISO-8859-1")   # converts the encoding
s.force_encoding("BINARY") # relabels the bytes without converting
s.unicode_normalize      # normalizes combining characters
"naïve".unicode_normalize(:nfd).length  #=> 6
"é".each_grapheme_cluster.to_a
```

Invalid byte sequences can be cleaned with `scrub`:

```ruby
"abc\xFF".scrub        #=> "abc\uFFFD"
```

---

## 5.8 Regular Expressions Basics

Regular expressions (`Regexp`) are written between slashes.

```ruby
"hello" =~ /ll/              #=> 2 (index of match, or nil)
"hello".match?(/ll/)         #=> true (fastest, no MatchData)
m = "John Smith".match(/(?<first>\w+) (?<last>\w+)/)
m[:first]                    #=> "John"
m["last"]                    #=> "Smith"
m.pre_match, m.post_match

"a1b22c333".scan(/\d+/)      #=> ["1", "22", "333"]
"2026-10-17".split("-")      #=> ["2026", "10", "17"]
"phone: 555-1234".sub(/(\d+)-(\d+)/, '\2-\1')   #=> "phone: 1234-555"
```

Common pieces: `.` any char, `\d` digit, `\w` word char, `\s` space, `\b` word boundary, `^` line start, `$` line end, `\A` string start, `\z` string end, `*` `+` `?` quantifiers, `{n,m}` range, `[abc]` set, `(a|b)` group, `/i` ignore case, `/x` extended, `/m` dot matches newline.

Use `\A` and `\z` (not `^` and `$`) when validating whole strings, since `^` and `$` match at each line.

---

[Previous](./[4]-Numbers-And-Math.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[6]-Symbols-Ranges-And-Nil.md)
