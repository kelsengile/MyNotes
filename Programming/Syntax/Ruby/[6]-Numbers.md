[Previous](./[5]-Variables-and-Constants.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[7]-Strings.md)

*Core Syntax*

# Lesson 6 - Numbers (Integer, Float, Rational, Complex, BigDecimal)

Ruby has several numeric classes, each suited to different jobs. All of them inherit from `Numeric`, so they share many methods.

---

## 6.1 Integers and Arbitrary Precision

`Integer` represents whole numbers, positive or negative.

```ruby
42
-7
1_000_000        # underscores make large numbers readable
0xff             # hexadecimal  => 255
0b1010           # binary       => 10
0o17             # octal        => 15  (also written 017)
```

### Arbitrary precision

Ruby integers grow as large as memory allows. There is no overflow.

```ruby
puts 2**100                   # 1267650600228229401496703205376
puts 100.downto(1).reduce(:*) # 100 factorial, a 158-digit number
puts (2**64).class            # Integer
```

### Integer arithmetic

```ruby
puts 7 + 2      # 9
puts 7 - 2      # 5
puts 7 * 2      # 14
puts 7 / 2      # 3     (integer division truncates toward negative infinity)
puts -7 / 2     # -4    (floors, not truncates toward zero!)
puts 7 % 2      # 1     (modulo)
puts -7 % 3     # 2     (result takes the sign of the divisor)
puts 7.remainder(-3) # 1  (result takes the sign of the dividend)
puts 2**10      # 1024  (exponent)
puts 2**-1      # (1/2) a Rational!
```

Other useful integer methods:

```ruby
7.divmod(2)          # [3, 1]   quotient and remainder
7.fdiv(2)            # 3.5      float division
10.pow(3, 7)         # 6        (10**3) % 7 computed efficiently
Integer.sqrt(17)     # 4        integer square root
10.gcd(4)            # 2        greatest common divisor
10.lcm(4)            # 20       least common multiple
255.to_s(2)          # "11111111"   convert to base 2
"ff".to_i(16)        # 255          parse from base 16
1234.digits          # [4, 3, 2, 1]   digits, least significant first
5.bit_length         # 3
```

Dividing by zero:

```ruby
# 5 / 0     # ZeroDivisionError
# 5 % 0     # ZeroDivisionError
```

---

## 6.2 Floats and Precision Issues

`Float` is a double-precision (64-bit) floating-point number.

```ruby
3.14
-0.5
2.5e3        # 2500.0 (scientific notation)
1e-4         # 0.0001
```

### Division gives a float when either side is a float

```ruby
puts 7 / 2       # 3
puts 7 / 2.0     # 3.5
puts 7.0 / 2     # 3.5
puts 7.to_f / 2  # 3.5
```

### The precision problem

Floats are stored in binary, so many decimals cannot be represented exactly. This is true in every language:

```ruby
puts 0.1 + 0.2             # 0.30000000000000004
puts 0.1 + 0.2 == 0.3      # false
```

Compare floats with a tolerance instead of `==`:

```ruby
a = 0.1 + 0.2
b = 0.3
puts (a - b).abs < Float::EPSILON      # true
puts a.round(10) == b.round(10)        # true
puts a.floor(2)                        # 0.3 (also works for digits)
```

### Special float values

```ruby
puts 1.0 / 0            # Infinity
puts -1.0 / 0           # -Infinity
puts (0.0 / 0).nan?     # true  (NaN means "not a number")
puts Float::INFINITY
puts Float::MAX         # 1.7976931348623157e+308
puts Float::MIN         # 2.2250738585072014e-308
puts Float::EPSILON     # 2.220446049250313e-16
puts Float::DIG         # 15 (reliable decimal digits)
```

Note that `1.0 / 0` is `Infinity`, but `1 / 0` (both integers) raises `ZeroDivisionError`.

### Rounding

```ruby
3.14159.round        # 3
3.14159.round(2)     # 3.14
3.5.round            # 4
2.5.round            # 3     (rounds half away from zero by default)
2.5.round(half: :even)   # 2 (banker's rounding)
3.7.floor            # 3     round down
3.2.ceil             # 4     round up
-3.7.truncate        # -3    drop the fraction
1234.567.round(-2)   # 1200  (negative digits round to the left)
```

---

## 6.3 Rational and Complex Numbers

### Rational: exact fractions

A `Rational` stores a numerator and denominator as integers, so there is no rounding error.

```ruby
r = Rational(3, 4)       # (3/4)
puts r                   # 3/4
puts 3/4r                # (3/4)   literal with an r suffix
puts 1/3r + 1/6r         # 1/2     exact!
puts Rational(1, 3) * 3  # 1/1
puts 0.75.to_r           # 3/4
puts 0.1.to_r            # 3602879701896397/36028797018963968 (the exact float value)
puts 0.1.rationalize(Rational(1, 100))   # 1/10   (simplest nearby fraction)
puts Rational("2/3")     # (2/3)
puts r.numerator         # 3
puts r.denominator       # 4
puts r.to_f              # 0.75
```

Use `Rational` when you need exact fractional math.

### Complex: numbers with an imaginary part

```ruby
c = Complex(3, 4)         # (3+4i)
puts 3 + 4i               # (3+4i)  literal with i suffix
puts c.real               # 3
puts c.imaginary          # 4  (also imag)
puts c.abs                # 5.0     magnitude
puts c.conjugate          # (3-4i)
puts c * Complex(1, 2)    # (-5+10i)
puts c.polar.inspect      # [5.0, 0.9272952180016122]  magnitude and angle
puts Complex.polar(1, Math::PI / 2).rectangular.inspect
puts Complex("1+2i")      # (1+2i)
```

For math with negative square roots, use the `CMath`-like approach with `Complex`:

```ruby
puts Complex(-4, 0) ** 0.5     # (1.2246467991473532e-16+2.0i)  approximately 2i
```

---

## 6.4 BigDecimal for Money

`Float` is a poor choice for money. `BigDecimal` stores decimal digits exactly. It is a **bundled gem**, so you load it explicitly:

```ruby
require "bigdecimal"
require "bigdecimal/util"     # adds to_d helpers

puts 0.1 + 0.2                                  # 0.30000000000000004
puts BigDecimal("0.1") + BigDecimal("0.2")      # 0.3e0
puts (BigDecimal("0.1") + BigDecimal("0.2")) == BigDecimal("0.3")   # true
```

> **Always create a `BigDecimal` from a string** (`BigDecimal("0.1")`), not a Float, or the float's error comes along.

With `bigdecimal/util`:

```ruby
puts "19.99".to_d          # 0.1999e2
puts 5.to_d                # 0.5e1
price = "19.99".to_d
total = price * 3
puts total.to_s("F")       # "59.97"  fixed-point string (not 0.5997e2)
puts total.to_f            # 59.97
puts total.round(1).to_s("F")    # "60.0"
```

Rounding modes:

```ruby
x = BigDecimal("2.5")
puts x.round(0, BigDecimal::ROUND_HALF_UP)     # 3
puts x.round(0, :half_even).to_i               # 2
```

> If you use Bundler (Lesson 3), add `gem "bigdecimal"` to your Gemfile in recent Ruby versions, because it is a bundled gem rather than part of the core.

Another popular technique is storing **money as integer cents** (`1999` for `$19.99`) and formatting only for display. Many professional systems prefer this, or use a library such as the `money` gem.

| Type | Exact? | Best for |
|------|--------|----------|
| `Integer` | Yes | Counting, IDs, cents |
| `Float` | No | Science, graphics, speed |
| `Rational` | Yes (fractions) | Exact fractional math |
| `BigDecimal` | Yes (decimals) | Money, accounting |
| `Complex` | Depends on parts | Engineering, math |

---

## 6.5 Number Methods and Conversions

### Checks and comparisons

```ruby
4.even?              # true
5.odd?               # true
0.zero?              # true
5.positive?          # true
-5.negative?         # true
5.between?(1, 10)    # true
15.clamp(1, 10)      # 10   limit to a range
5.clamp(..3)         # 3
5 <=> 7              # -1  (also 0 or 1)
3.7.nan?             # false
5.integer?           # true
5.0.integer?         # false
1.5.finite?          # true
```

### Absolute value, sign, succession

```ruby
-5.abs               # 5
5.succ               # 6   (next)
5.pred               # 4   (previous)
5.0.floor            # 5
-5.pred              # -6
```

### Conversions

```ruby
5.to_f               # 5.0
5.9.to_i             # 5      truncates
"42".to_i            # 42
"3.7abc".to_f        # 3.7
"abc".to_i           # 0      (silently!)
"12abc".to_i         # 12
5.to_s               # "5"
5.to_r               # (5/1)
5.to_c               # (5+0i)
65.chr               # "A"
```

**Strict conversion** raises an error on bad input, which is safer for user input:

```ruby
Integer("42")        # 42
Integer("0x1A", 16)  # 26
Integer("abc")       # ArgumentError: invalid value for Integer(): "abc"
Integer("abc", exception: false)   # nil
Float("3.5")         # 3.5
Float("x", exception: false)       # nil
```

### Random numbers

```ruby
rand               # a float from 0.0 up to (not including) 1.0
rand(10)           # an integer from 0 to 9
rand(1..6)         # an integer from 1 to 6 (like a die)
Random.new(42).rand(100)   # reproducible with a seed
```

### Formatting numbers for display

```ruby
puts 3.14159.round(2)                       # 3.14
puts format("%.2f", 3.14159)                # 3.14
puts format("%05d", 42)                     # 00042
puts 1234567.to_s.reverse.scan(/\d{1,3}/).join(",").reverse   # 1,234,567
```

### Math module

```ruby
Math.sqrt(16)       # 4.0
Math.cbrt(27)       # 3.0
Math.hypot(3, 4)    # 5.0
Math.sin(Math::PI / 2)   # 1.0
Math.log(Math::E)   # 1.0
Math.log10(1000)    # 3.0
Math.log2(8)        # 3.0
Math::PI            # 3.141592653589793
```

### Iteration with numbers

```ruby
3.times { |i| puts i }          # 0 1 2
1.upto(3) { |i| puts i }        # 1 2 3
3.downto(1) { |i| puts i }      # 3 2 1
1.step(10, 3) { |i| puts i }    # 1 4 7 10
```

---

[Previous](./[5]-Variables-and-Constants.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[7]-Strings.md)