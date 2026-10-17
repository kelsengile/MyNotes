[Previous](./[3]-Variables-And-Constants.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[5]-Strings.md)

*Basics*

# Lesson 4 - Numbers And Math

## 4.1 Integers

`Integer` represents whole numbers of **arbitrary size**: Ruby automatically switches to big numbers when needed.

```ruby
42
-7
1_000_000          # underscores improve readability
0b1010             #=> 10 (binary)
0o17               #=> 15 (octal)
0xff               #=> 255 (hexadecimal)
2**100             #=> 1267650600228229401496703205376
```

Useful methods:

```ruby
10.even?           #=> true
7.odd?             #=> true
-4.abs             #=> 4
5.pred             #=> 4
5.succ             #=> 6
255.to_s(2)        #=> "11111111"
"ff".to_i(16)      #=> 255
1234.digits        #=> [4, 3, 2, 1]
10.gcd(4)          #=> 2
10.lcm(4)          #=> 20
5.clamp(1, 3)      #=> 3
3.14.to_i          #=> 3
Integer.sqrt(17)   #=> 4
```

---

## 4.2 Floats

`Float` holds double-precision floating-point numbers.

```ruby
3.14
-0.5
1.5e3              #=> 1500.0
Float::INFINITY
Float::NAN
Float::EPSILON
```

Floats cannot represent every decimal exactly:

```ruby
0.1 + 0.2              #=> 0.30000000000000004
(0.1 + 0.2).round(2)   #=> 0.3
(0.1 + 0.2 - 0.3).abs < Float::EPSILON   #=> true
```

Never compare floats with `==` when they come from arithmetic; use a tolerance or `round`. For exact decimal math (money), use `Rational` or `BigDecimal` (below) or store cents as integers.

---

## 4.3 Arithmetic Operators

```ruby
7 + 2      #=> 9
7 - 2      #=> 5
7 * 2      #=> 14
7 / 2      #=> 3      (integer division when both are integers)
7 / 2.0    #=> 3.5
7 % 3      #=> 1      (modulo)
2 ** 10    #=> 1024   (exponent)
2 ** -1    #=> (1/2)  (a Rational)
7.fdiv(2)  #=> 3.5    (float division)
7.divmod(2)#=> [3, 1] (quotient and remainder)
```

Integer division **floors** toward negative infinity, and the sign of `%` follows the divisor:

```ruby
-7 / 2        #=> -4
-7 % 3        #=> 2
-7.remainder(3) #=> -1   (sign follows the dividend)
```

Dividing an integer by zero raises `ZeroDivisionError`; dividing a float by zero gives `Infinity` or `NaN`.

---

## 4.4 Rounding And Conversion

```ruby
3.7.floor      #=> 3
3.2.ceil       #=> 4
3.5.round      #=> 4
2.5.round      #=> 3      (rounds half away from zero)
2.5.round(half: :even) #=> 2
3.14159.round(2)  #=> 3.14
1234.round(-2)    #=> 1200
3.99.truncate     #=> 3
-3.7.truncate     #=> -3
```

Converting from strings:

```ruby
"42".to_i          #=> 42
"42abc".to_i       #=> 42    (stops at first invalid char)
"abc".to_i         #=> 0     (silent failure)
"3.5".to_f         #=> 3.5
Integer("42")      #=> 42
Integer("abc")     # ArgumentError (strict)
Float("3.5")       #=> 3.5
Integer("abc", exception: false)   #=> nil
```

Prefer `Integer()` and `Float()` when you need to validate user input.

---

## 4.5 Rational And Complex Numbers

```ruby
r = Rational(1, 3)
r + Rational(1, 6)   #=> (1/2)
3r / 4               #=> (3/4)   (literal suffix r)
0.75.to_r            #=> (3/4)

c = Complex(2, 3)
c * c                #=> (-5+12i)
3 + 2i               # literal suffix i
c.abs                #=> 3.605551275463989
```

`Rational` keeps exact fractions, avoiding float error.

---

## 4.6 BigDecimal

`BigDecimal` provides arbitrary-precision decimal arithmetic. It ships as a bundled gem, so add it to your `Gemfile` (`gem "bigdecimal"`) in a Bundler project.

```ruby
require "bigdecimal"
require "bigdecimal/util"

price = BigDecimal("0.1") + BigDecimal("0.2")
price == BigDecimal("0.3")   #=> true
price.to_s("F")              #=> "0.3"
"19.99".to_d                 #=> 0.1999e2
```

---

## 4.7 The Math Module

```ruby
Math::PI           #=> 3.141592653589793
Math::E
Math.sqrt(16)      #=> 4.0
Math.cbrt(27)      #=> 3.0
Math.sin(Math::PI / 2)  #=> 1.0
Math.log(Math::E)       #=> 1.0
Math.log10(1000)        #=> 3.0
Math.log2(8)            #=> 3.0
Math.hypot(3, 4)        #=> 5.0
```

`Math.sqrt(-1)` raises `Math::DomainError`.

---

## 4.8 Iterating And Random Numbers

Integers have friendly iterator methods:

```ruby
3.times { |i| print i, " " }     # 0 1 2
1.upto(4) { |i| print i, " " }   # 1 2 3 4
4.downto(1) { |i| print i, " " } # 4 3 2 1
0.step(10, 5) { |i| print i, " " } # 0 5 10
```

Random numbers:

```ruby
rand               # float 0.0 <= x < 1.0
rand(10)           # integer 0..9
rand(1..6)         # integer 1..6
Random.new(42).rand(100)   # reproducible with a seed
[1, 2, 3].sample           # random element
[1, 2, 3].shuffle          # random order
require "securerandom"
SecureRandom.hex(8)        # secure random token
```

Use `SecureRandom` (not `rand`) for passwords, tokens, and anything security related.

---

[Previous](./[3]-Variables-And-Constants.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[5]-Strings.md)
