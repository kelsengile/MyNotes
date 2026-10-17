[Previous](./[20]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[22]-Classes-and-Objects.md)

*Core Syntax*

# Lesson 21 - Regular Expressions

A **regular expression** (regex) describes a text pattern. You use it to test whether text matches, to extract parts of it, and to find and replace. Ruby has regexes built into the language.

---

## 21.1 Regex Literals and Matching

A regex literal is written between slashes. Its class is `Regexp`.

```ruby
/ruby/                  # matches the text "ruby"
%r{https?://}           # %r{} avoids escaping slashes in the pattern
Regexp.new("ruby")      # build from a string (useful for dynamic patterns)
```

### Testing for a match

```ruby
"I love ruby" =~ /ruby/        # 7   (index of the match, or nil)
"I love ruby" !~ /python/      # true
/ruby/.match?("I love ruby")   # true  (just true/false, fastest)
"I love ruby".match?(/ruby/)   # true
"I love ruby".match(/r\w+/)    # #<MatchData "ruby">   (or nil)
/ruby/ === "ruby!"             # true  (used by case/when)
```

Because a failed `match` and `=~` give `nil` (falsy), they work in conditions:

```ruby
if email.match?(/@/)
  puts "looks like an email"
end

case input
when /^\d+$/ then puts "digits only"
when /^[a-z]+$/i then puts "letters only"
end
```

### Flags (options) written after the closing slash

| Flag | Meaning |
|------|---------|
| `i` | Ignore case |
| `m` | Make `.` also match newlines (multiline mode) |
| `x` | Extended: ignore whitespace and allow `#` comments in the pattern |
| `o` | Interpolate `#{}` only once |
| `u` | Treat as UTF-8 |

```ruby
"RUBY" =~ /ruby/i          # 0

phone = /
  (\d{3})   # area code
  [-.\s]?   # separator
  (\d{4})   # number
/x
```

### Interpolation

```ruby
word = "ruby"
/#{word}/.match?("I love ruby")        # true

# Escape user input so special characters lose their meaning!
user_input = "1+1=2"
/#{Regexp.escape(user_input)}/.match?("is 1+1=2 true?")   # true
Regexp.union("cat", "dog")             # /cat|dog/
Regexp.union(/a/i, "b.c")              # combines patterns; escapes the string
```

---

## 21.2 Character Classes, Anchors, and Quantifiers

### Ordinary characters and the dot

Most characters match themselves. These characters are special and need a backslash to match literally: `. ^ $ | ? * + ( ) [ ] { } \`.

```ruby
/a.c/.match?("abc")      # true   (. matches any one character except a newline)
/a\.c/.match?("abc")     # false  (\. is a real dot)
/a\.c/.match?("a.c")     # true
```

### Character classes

| Pattern | Matches |
|---------|---------|
| `[abc]` | One of `a`, `b`, `c` |
| `[a-z]`, `[0-9]`, `[A-Za-z]` | A character in the range |
| `[^abc]` | Any character **except** a, b, c |
| `\d` / `\D` | A digit / a non-digit |
| `\w` / `\W` | A word character (`[a-zA-Z0-9_]`) / a non-word character |
| `\s` / `\S` | Whitespace (space, tab, newline) / non-whitespace |
| `\h` | A hexadecimal digit |
| `.` | Any character except newline (with `/m`, newline too) |
| `[[:alpha:]]`, `[[:digit:]]`, `[[:upper:]]`, `[[:space:]]`, `[[:punct:]]` | POSIX classes (Unicode-aware) |
| `\p{L}`, `\p{Han}`, `\p{Upper}` | Unicode properties (letters, scripts, ...) |

```ruby
"abc123".scan(/\d/)             # ["1", "2", "3"]
"hello world".scan(/[aeiou]/)   # ["e", "o", "o"]
"über".match?(/\A[[:alpha:]]+\z/)   # true (Unicode-aware)
"日本語" =~ /\p{Han}/             # 0
```

### Anchors: where the match must be

| Anchor | Meaning |
|--------|---------|
| `^` | Start of a **line** |
| `$` | End of a **line** |
| `\A` | Start of the **whole string** |
| `\z` | End of the whole string |
| `\Z` | End of the string, or before a final newline |
| `\b` | A word boundary |
| `\B` | Not a word boundary |

```ruby
"cat concat".scan(/\bcat\b/)     # ["cat"]   (not the "cat" inside "concat")
```

> **Important:** in Ruby `^` and `$` match at every **line** boundary, not just the start and end of the string. For validating input, always use **`\A` and `\z`**:

```ruby
input = "abc\n<script>"
input.match?(/^[a-z]+$/)       # true   (matches the first line only!) -- unsafe
input.match?(/\A[a-z]+\z/)     # false  -- correct
```

### Quantifiers: how many times

| Quantifier | Meaning |
|------------|---------|
| `*` | 0 or more |
| `+` | 1 or more |
| `?` | 0 or 1 (optional) |
| `{3}` | Exactly 3 |
| `{2,}` | 2 or more |
| `{2,4}` | Between 2 and 4 |

```ruby
/colou?r/.match?("color")     # true
/\d{3}-\d{4}/.match?("555-1234")   # true
"aaa".match(/a+/)[0]          # "aaa"   (greedy: takes as much as possible)
```

### Greedy versus lazy

Quantifiers are **greedy** by default. Add `?` to make them **lazy** (take as little as possible):

```ruby
html = "<b>bold</b> and <i>italic</i>"
html[/<.+>/]       # "<b>bold</b> and <i>italic</i>"   (greedy)
html[/<.+?>/]      # "<b>"                            (lazy)
html.scan(/<.+?>/) # ["<b>", "</b>", "<i>", "</i>"]
```

(There are also **possessive** quantifiers like `a++` that never give back characters, useful for performance.)

### Alternation and grouping

```ruby
/cat|dog/.match?("hotdog")        # true
/gr(a|e)y/.match?("grey")         # true   (parentheses group)
/(ab)+/.match("ababab")[0]        # "ababab"
```

---

## 21.3 Capture Groups and Named Captures

Parentheses **capture** the matched part for later use.

```ruby
m = "John Smith".match(/(\w+) (\w+)/)
m[0]     # "John Smith"   (the whole match)
m[1]     # "John"
m[2]     # "Smith"
```

### Named captures

Use `(?<name>...)` for readable code:

```ruby
m = "2026-10-17".match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/)
m[:year]           # "2026"
m["month"]         # "10"
m[:day].to_i       # 17
m.names            # ["year", "month", "day"]
m.named_captures   # {"year"=>"2026", "month"=>"10", "day"=>"17"}
```

If a regex **literal** with named groups is on the **left** of `=~`, local variables are created automatically:

```ruby
if /(?<user>\w+)@(?<host>[\w.]+)/ =~ "ada@example.com"
  puts user      # ada
  puts host      # example.com
end
```

### Non-capturing groups

`(?:...)` groups without capturing (faster and keeps the numbering clean):

```ruby
"abab".match(/(?:ab)+/)[0]       # "abab"
"ab12".match(/(?:ab)(\d+)/)[1]   # "12"
```

### Backreferences: reuse a captured group inside the pattern

```ruby
/(\w)\1/.match?("hello")             # true   (the double "l")
/(?<q>["']).*?\k<q>/.match('say "hi"')[0]   # "\"hi\""   (same quote at both ends)
```

### Lookahead and lookbehind (match without consuming)

| Syntax | Meaning |
|--------|---------|
| `(?=...)` | Followed by ... |
| `(?!...)` | **Not** followed by ... |
| `(?<=...)` | Preceded by ... |
| `(?<!...)` | **Not** preceded by ... |

```ruby
"price: $45 and 30 euros".scan(/(?<=\$)\d+/)      # ["45"]
"price: $45 and 30 euros".scan(/\d+(?= euros)/)   # ["30"]
"foobar foobaz".scan(/foo(?!bar)\w+/)             # ["foobaz"]
```

---

## 21.4 match, scan, sub, and gsub

### match: the first match as MatchData

```ruby
m = "Order #4521 shipped".match(/#(\d+)/)
m[1]                    # "4521"
"no digits".match(/\d+/)   # nil
"Order #4521"[/#(\d+)/, 1]  # "4521"   (String#[] with a regex and a group number)
"abc".match(/b/) { |md| md.pre_match }   # "a"   (block form)
```

### scan: all matches

Without groups, it returns the matched strings. With groups, it returns an array of group arrays:

```ruby
"a1 b22 c333".scan(/\d+/)           # ["1", "22", "333"]
"a1 b22".scan(/([a-z])(\d+)/)       # [["a", "1"], ["b", "22"]]
"a1 b22".scan(/[a-z]\d+/) { |s| puts s }   # block form prints each

"k1=v1;k2=v2".scan(/(\w+)=(\w+)/).to_h   # {"k1"=>"v1", "k2"=>"v2"}
```

### sub and gsub: replace

`sub` replaces the **first** match, `gsub` replaces **all** matches. Bang versions (`sub!`, `gsub!`) change the string in place (and return `nil` if nothing changed).

```ruby
"hello world".sub(/o/, "0")        # "hell0 world"
"hello world".gsub(/o/, "0")       # "hell0 w0rld"
"hello".gsub(/[aeiou]/, "*")       # "h*ll*"
"  too   many   spaces ".gsub(/\s+/, " ").strip   # "too many spaces"
```

Using captures in the replacement text:

```ruby
"John Smith".sub(/(\w+) (\w+)/, '\2, \1')                     # "Smith, John"
"2026-10-17".sub(/(?<y>\d+)-(?<m>\d+)-(?<d>\d+)/, '\k<d>/\k<m>/\k<y>')   # "17/10/2026"
```

Use **single quotes** for replacement strings, or double the backslashes in double-quoted strings (`"\\2, \\1"`).

A **block** computes each replacement (`$~` and `$1` are set inside):

```ruby
"3 apples and 4 pears".gsub(/\d+/) { |n| (n.to_i * 2).to_s }
# "6 apples and 8 pears"

"hello world".gsub(/\b\w/) { |c| c.upcase }     # "Hello World"
```

A **hash** looks up replacements:

```ruby
"cat dog".gsub(/cat|dog/, "cat" => "feline", "dog" => "canine")   # "feline canine"
```

### Other string methods that accept regexes

```ruby
"a1b22c".split(/\d+/)           # ["a", "b", "c"]
"2026-10-17".split(/-/, 2)      # ["2026", "10-17"]
"hello".index(/l+/)             # 2
"hello".start_with?(/he/)       # true
"abc".slice(/b./)               # "bc"
%w[apple banana cherry].grep(/an/)    # ["banana"]
%w[apple banana].grep_v(/an/)         # ["apple"]
"hello world".partition(/o/)    # ["hell", "o", " world"]
```

---

## 21.5 Special Variables and MatchData

After a successful match, Ruby stores details in **special variables** (they are local to the current method and thread):

| Variable | Contents |
|----------|----------|
| `$~` | The `MatchData` object of the last match |
| `$&` | The whole matched text |
| `` $` `` | Text before the match |
| `$'` | Text after the match |
| `$1`, `$2`, ... | Contents of capture groups |
| `Regexp.last_match` | Same as `$~` (and `Regexp.last_match(1)` is `$1`) |

```ruby
if "Hi John Smith!" =~ /(\w+) (\w+)/
  puts $~[0]      # John Smith
  puts $1         # John
  puts $2         # Smith
  puts $`         # Hi 
  puts $'         # !
end
```

They are `nil` after a **failed** match. `match?` does **not** set them. Because they are "magic", prefer the explicit `MatchData` from `match` in new code.

### MatchData

```ruby
m = "Name: Ada, Age: 36".match(/Name: (?<name>\w+), Age: (?<age>\d+)/)

m[0]               # "Name: Ada, Age: 36"
m[:name]           # "Ada"
m[2]               # "36"
m.captures         # ["Ada", "36"]
m.named_captures   # {"name"=>"Ada", "age"=>"36"}
m.names            # ["name", "age"]
m.to_a             # [whole, "Ada", "36"]
m.values_at(1, 2)  # ["Ada", "36"]
m.pre_match        # ""
m.post_match       # ""
m.begin(0)         # 0   (start index of the whole match)
m.end(:name)       # 9   (end index of a group)
m.offset(:age)     # [16, 18]
m.string           # the original string
m.regexp           # the regex used
```

`MatchData` also works with pattern matching (Lesson 19):

```ruby
case "2026-10-17".match(/(?<y>\d+)-(?<m>\d+)/)
in { y:, m: }
  puts "year #{y}, month #{m}"
end
```

### Useful `Regexp` methods

```ruby
re = /(\d+)-(\d+)/
re.source         # "(\\d+)-(\\d+)"
re.options        # numeric flags
re.names          # []
re.match?("1-2")  # true
Regexp.escape("a.b*c")   # "a\\.b\\*c"
Regexp.timeout = 1.0     # set a global time limit for matching (seconds)
```

---

## 21.6 Common Regex Pitfalls

### 1. `^` and `$` are line anchors

Use `\A` and `\z` to validate a whole string (see 21.2). This is a well-known source of security bugs in Ruby code.

### 2. Forgetting to escape special characters

```ruby
"3x14".match?(/3.14/)         # true  (the dot matched "x")
"3x14".match?(/3\.14/)        # false
```

And always `Regexp.escape` text that comes from a user or a variable.

### 3. Greedy matching grabs too much

```ruby
'say "a" and "b"'[/".*"/]      # "\"a\" and \"b\""
'say "a" and "b"'[/".*?"/]     # "\"a\""
'say "a" and "b"'[/"[^"]*"/]   # "\"a\""   (often the best: no backtracking)
```

### 4. `.` does not match a newline

Use the `m` flag (`/.../m`) if you want it to, or `[\s\S]`.

### 5. Using `match` when you only need true/false

`match?` is faster and does not allocate a `MatchData` or touch `$~`.

### 6. Misusing the result of `=~`

`=~` returns an **index** (or `nil`), which can be `0`. In Ruby `0` is truthy, so `if str =~ /x/` works, but don't treat the number as a boolean elsewhere. Prefer `match?` when you only need yes/no.

### 7. Catastrophic backtracking (ReDoS)

Nested, overlapping quantifiers can take exponential time on certain input:

```ruby
/(a+)+$/.match?("a" * 40 + "b")    # may take extremely long on older Ruby versions
```

Defenses: simplify patterns, avoid nested quantifiers like `(a+)+` and `(.*)*`, use possessive quantifiers or atomic groups `(?>...)`, set a timeout (`Regexp.timeout = 1.0` or `Regexp.new(pattern, timeout: 1.0)`), and don't run complicated user-supplied patterns. Modern Ruby versions memoize many matches to avoid the worst cases, but you should still be careful.

### 8. Don't parse everything with regexes

Regexes are great for simple patterns, but not for nested structures such as HTML, JSON, or full email address validation. Use a real parser (`Nokogiri`, `JSON`, `URI`), or for emails, a simple check and a confirmation message.

```ruby
# Reasonable "looks like an email" check:
EMAIL = /\A[^@\s]+@[^@\s]+\.[^@\s]+\z/
"ada@example.com".match?(EMAIL)    # true
```

### 9. Replacement string backslashes

A backslash is special in replacement strings, so inserting a literal one is awkward. The block form avoids the escaping problem:

```ruby
"path".gsub("a") { "\\" }     # "p\\th"  (a single backslash is inserted)
```

### 10. Readability

Long regexes are hard to read. Use `/x` with comments, named groups, and constants:

```ruby
DATE = /
  (?<year>\d{4})  -  # four digits
  (?<month>\d{2}) -  # two digits
  (?<day>\d{2})      # two digits
/x
```

Test patterns interactively in IRB, or at sites like [rubular.com](https://rubular.com), before using them in programs.

---

[Previous](./[20]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[22]-Classes-and-Objects.md)