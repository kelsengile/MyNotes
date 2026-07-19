# Text Processing

## 13.1 Regular Expressions (Regex)

A regular expression is a sequence of characters that defines a search pattern, used for matching, extracting, and manipulating text. Regex is supported (with minor syntax variations) in nearly every programming language, as well as in tools like `grep`, `sed`, and text editors.

### Core Syntax

| Symbol | Meaning | Example | Matches |
|---|---|---|---|
| `.` | Any character (except newline) | `a.c` | `abc`, `a1c` |
| `^` | Start of string/line | `^Hello` | `Hello world` |
| `$` | End of string/line | `world$` | `Hello world` |
| `*` | 0 or more of preceding token | `ab*` | `a`, `ab`, `abbb` |
| `+` | 1 or more of preceding token | `ab+` | `ab`, `abbb` (not `a`) |
| `?` | 0 or 1 of preceding token (optional) | `colou?r` | `color`, `colour` |
| `{n,m}` | Between n and m repetitions | `a{2,4}` | `aa`, `aaa`, `aaaa` |
| `[]` | Character class (any one of) | `[aeiou]` | any single vowel |
| `[^]` | Negated character class | `[^0-9]` | any non-digit |
| `\|` | Alternation (OR) | `cat\|dog` | `cat`, `dog` |
| `()` | Grouping / capturing group | `(ab)+` | `ab`, `abab` |
| `(?:)` | Non-capturing group | `(?:ab)+` | groups without capturing |

### Common Shorthand Character Classes

| Shorthand | Equivalent | Meaning |
|---|---|---|
| `\d` | `[0-9]` | Digit |
| `\D` | `[^0-9]` | Non-digit |
| `\w` | `[A-Za-z0-9_]` | Word character |
| `\W` | `[^A-Za-z0-9_]` | Non-word character |
| `\s` | `[ \t\n\r\f\v]` | Whitespace |
| `\S` | `[^ \t\n\r\f\v]` | Non-whitespace |
| `\b` | — | Word boundary |
| `\B` | — | Non-word boundary |

### Anchors, Lookahead, and Lookbehind

- **Anchors** (`^`, `$`, `\b`) match a *position* in the string, not actual characters.
- **Lookahead** `(?=...)` — asserts what follows without consuming it: `foo(?=bar)` matches `foo` only if followed by `bar`.
- **Negative lookahead** `(?!...)` — asserts what does *not* follow: `foo(?!bar)`.
- **Lookbehind** `(?<=...)` — asserts what precedes: `(?<=\$)\d+` matches digits only if preceded by `$`.
- **Negative lookbehind** `(?<!...)` — asserts what does *not* precede.

```
Example: Extract price without the currency symbol
Input:  "$42.50"
Regex:  (?<=\$)\d+\.\d+
Match:  "42.50"
```

### Greedy vs. Lazy Matching

By default, quantifiers (`*`, `+`, `{n,m}`) are **greedy** — they match as much as possible. Adding `?` after a quantifier makes it **lazy** — matching as little as possible.

```
Input:  <b>bold</b> and <i>italic</i>
Greedy: <.+>        → matches the ENTIRE string (too much)
Lazy:   <.+?>        → matches "<b>", "</b>", "<i>", "</i>" separately
```

### Capturing Groups and Backreferences

```python
import re

match = re.search(r"(\d{4})-(\d{2})-(\d{2})", "Date: 2026-07-18")
year, month, day = match.groups()
# year = "2026", month = "07", day = "18"
```

- **Named groups** improve readability: `(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})` in Python, or `(?<year>\d{4})` in JavaScript/.NET.
- **Backreferences** refer to a previously captured group within the same pattern, e.g. `(\w+)\s+\1` matches repeated words like "the the".

### Practical Examples

```
Email (simplified):     ^[\w.+-]+@[\w-]+\.[a-zA-Z]{2,}$
US phone number:        \(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}
URL (simplified):       https?://[^\s/$.?#].[^\s]*
Hex color code:         #(?:[0-9a-fA-F]{3}){1,2}
Whitespace trim:        ^\s+|\s+$
Extract hashtags:       #\w+
IPv4 address:           \b(?:\d{1,3}\.){3}\d{1,3}\b
```

### Language / Tool Usage

```javascript
// JavaScript
const re = /\d+/g;
"a1 b22 c333".match(re);      // ["1", "22", "333"]
"hello world".replace(/o/g, "0"); // "hell0 w0rld"
```

```python
# Python
import re
re.findall(r"\d+", "a1 b22 c333")        # ['1', '22', '333']
re.sub(r"o", "0", "hello world")          # 'hell0 w0rld'
re.split(r"\s*,\s*", "a, b,c ,  d")       # ['a', 'b', 'c', 'd']
```

```bash
# Command line
grep -E '^[0-9]+$' file.txt      # lines that are only digits
sed 's/foo/bar/g' file.txt       # replace all "foo" with "bar"
```

### Best Practices and Pitfalls

- **Don't overuse regex** — for deeply nested or recursive structures (like HTML/JSON), use a proper parser instead; regex struggles with nested patterns.
- **Beware of catastrophic backtracking** — poorly constructed patterns with nested quantifiers (e.g., `(a+)+$`) can cause exponential slowdowns on certain inputs (ReDoS). Prefer atomic groups or possessive quantifiers where supported, or restructure the pattern.
- **Escape special characters** when matching them literally: `.`, `*`, `+`, `?`, `(`, `)`, `[`, `]`, `{`, `}`, `^`, `$`, `\`, `|` all need a backslash (`\.`) to be treated literally.
- **Test incrementally** — build complex patterns piece by piece and test against edge cases (empty strings, unusual whitespace, unicode).
- **Use raw strings** in Python (`r"..."`) to avoid double-escaping backslashes.
- **Compile once, reuse** — if a regex is used repeatedly (e.g., in a loop), compile it once (`re.compile()` in Python) rather than recompiling on every iteration.

## 13.2 String Parsing & Manipulation Patterns

Beyond regex, most text-processing tasks rely on a common toolbox of string operations and parsing strategies.

### Core String Operations

| Operation | Purpose | Example |
|---|---|---|
| `split()` | Break a string into a list by delimiter | `"a,b,c".split(",")` → `["a","b","c"]` |
| `join()` | Combine a list into a string | `",".join(["a","b","c"])` → `"a,b,c"` |
| `strip()`/`trim()` | Remove leading/trailing whitespace | `"  hi  ".strip()` → `"hi"` |
| `replace()` | Substitute substrings | `"hi".replace("h","H")` → `"Hi"` |
| `slice`/substring | Extract part of a string | `"hello"[1:4]` → `"ell"` |
| `upper()`/`lower()` | Change case | `"Hi".lower()` → `"hi"` |
| `startswith()`/`endswith()` | Prefix/suffix check | `"file.txt".endswith(".txt")` → `True` |
| `find()`/`indexOf()` | Locate a substring's position | `"hello".find("l")` → `2` |
| `format()`/f-strings | Template/interpolate values | `f"Hi {name}"` |

### Tokenization

Breaking text into meaningful units ("tokens") — words, punctuation, numbers, etc. — is the foundation of parsers, compilers, and NLP pipelines.

```python
# Simple whitespace tokenizer
"The quick brown fox".split()
# ['The', 'quick', 'brown', 'fox']

# Regex-based tokenizer (splits on word boundaries)
import re
re.findall(r"\w+|[^\w\s]", "Hello, world!")
# ['Hello', ',', 'world', '!']
```

### Parsing Structured Text

Different formats call for different parsing strategies:

- **Delimiter-based formats** (CSV, TSV) — split on a known delimiter, but watch for quoted fields containing the delimiter itself (use a dedicated CSV library rather than naive `split(",")`).
- **Key-value formats** (`.env`, `.ini`, query strings) — split on `=` or `:`, then split entries on newlines or `&`.
- **Hierarchical formats** (JSON, XML, YAML) — use a proper parser/library (`json.loads`, `xml.etree`, `yaml.safe_load`) rather than hand-rolled string splitting; these formats have escaping and nesting rules that are error-prone to reimplement.
- **Fixed-width formats** — parse by character position/column ranges rather than delimiters.
- **Custom grammars** (e.g., a config DSL or small language) — for anything beyond simple structure, consider a proper lexer/parser (tokenizer + parser combinator, or a library like ANTLR, PLY, or a hand-written recursive-descent parser) rather than a tangle of regex.

### Common Manipulation Patterns

**Building strings efficiently:**
```python
# Inefficient: repeated concatenation creates a new string each time
result = ""
for word in words:
    result += word + " "

# Efficient: build a list, join once
result = " ".join(words)
```

**Normalizing text** (common before comparison, search, or storage):
```python
text = text.strip().lower()
text = re.sub(r"\s+", " ", text)          # collapse multiple spaces
text = unicodedata.normalize("NFKC", text) # normalize unicode forms
```

**Template substitution:**
```python
# f-strings / template literals
name, count = "Alice", 3
f"{name} has {count} items"          # Python
`${name} has ${count} items`         # JavaScript
```

**Padding and alignment:**
```python
"42".zfill(5)        # "00042"
"42".rjust(5, "0")   # "00042"
"hi".ljust(5, "-")   # "hi---"
```

**Chunking / windowing text:**
```python
def chunks(s, size):
    return [s[i:i+size] for i in range(0, len(s), size)]

chunks("abcdefgh", 3)  # ['abc', 'def', 'gh']
```

### State Machines for Parsing

For text with structure that regex can't cleanly express (nested brackets, quoted strings with escapes), a simple state machine — tracking "am I inside a quote / comment / tag right now?" — is often clearer and more robust than a single regex.

```python
def parse_quoted_csv_line(line):
    fields, current, in_quotes = [], "", False
    for char in line:
        if char == '"':
            in_quotes = not in_quotes
        elif char == "," and not in_quotes:
            fields.append(current)
            current = ""
        else:
            current += char
    fields.append(current)
    return fields
```

### Best Practices

- **Prefer built-in/standard-library parsers** (CSV, JSON, XML, URL parsing) over hand-rolled string splitting — they correctly handle escaping, quoting, and edge cases you're likely to miss.
- **Normalize early** — trim whitespace and standardize case/encoding as close to the input boundary as possible, so downstream logic can assume clean data.
- **Validate assumptions** — check that a split produced the expected number of parts before indexing into it, to avoid `IndexError`/`undefined` bugs on malformed input.
- **Mind encoding** — always be explicit about text encoding (UTF-8, etc.) when reading/writing files to avoid `UnicodeDecodeError`s or mojibake.
- **Immutable strings** — in languages where strings are immutable (Python, Java, JavaScript), be aware that manipulation creates new strings; use builders (`StringBuilder`, list + `join`) for heavy concatenation in loops.
- **Separate parsing from processing** — parse raw text into a structured representation (dict, object, list) first, then operate on that structure, rather than repeatedly re-parsing the same string.