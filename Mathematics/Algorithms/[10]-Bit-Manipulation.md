[Previous](./[9]-String-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md)

# Lesson 10 - Bit Manipulation

## 10.1 Bitwise Operators Recap

Bit manipulation means working directly with the individual binary digits (bits) that make up a number, using **bitwise operators**. These operators are extremely fast — often a single CPU instruction — which is why bit tricks show up in performance-critical code, low-level systems programming, and as clever shortcuts in algorithm problems.

| Operator | Symbol | What it does |
|----------|--------|---------------|
| AND      | `&`    | 1 only where **both** bits are 1 |
| OR       | `\|`    | 1 where **at least one** bit is 1 |
| XOR      | `^`    | 1 where the bits **differ** |
| NOT      | `~`    | Flips every bit |
| Left shift | `<<` | Shifts bits left, filling with 0s (multiplies by 2 per shift) |
| Right shift | `>>` | Shifts bits right (divides by 2 per shift, for positive numbers) |

```python
a = 0b1010  # 10
b = 0b0110  # 6

print(bin(a & b))   # 0b10   (2)
print(bin(a | b))   # 0b1110 (14)
print(bin(a ^ b))   # 0b1100 (12)
print(bin(a << 1))  # 0b10100 (20) -- same as a * 2
print(bin(a >> 1))  # 0b101   (5)  -- same as a // 2
```

A property worth knowing well: **XOR** is its own inverse. `x ^ y ^ y == x` for any x and y. This makes XOR useful for swapping values without a temporary variable, and for the "find the unique element" trick below.

---

## 10.2 Common Bit Tricks (Masks, Checking/Setting Bits)

A **bitmask** is a number used, together with a bitwise operator, to read or modify specific bits of another number while leaving the rest untouched.

```python
# Check if the i-th bit is set (counting from 0 at the rightmost bit)
def get_bit(num, i):
    return (num >> i) & 1

# Set the i-th bit to 1
def set_bit(num, i):
    return num | (1 << i)

# Clear the i-th bit (set it to 0)
def clear_bit(num, i):
    return num & ~(1 << i)

# Toggle (flip) the i-th bit
def toggle_bit(num, i):
    return num ^ (1 << i)

n = 0b1010  # 10
print(get_bit(n, 1))     # 1  (bit at index 1 is set)
print(bin(set_bit(n, 0)))    # 0b1011 (11)
print(bin(clear_bit(n, 1)))  # 0b1000 (8)
print(bin(toggle_bit(n, 3))) # 0b0010 (2)
```

**Checking if a number is even or odd**, without using `%`:

```python
def is_even(n):
    return (n & 1) == 0
```

**Counting the number of set (1) bits**, using Brian Kernighan's trick — `n & (n - 1)` clears the lowest set bit, so counting how many times you can do that before reaching 0 gives the count:

```python
def count_set_bits(n):
    count = 0
    while n:
        n &= (n - 1)
        count += 1
    return count

print(count_set_bits(0b10110))  # 3
```

**Checking if a number is a power of two** — a power of two has exactly one set bit, so `n & (n - 1)` clears that single bit, leaving 0:

```python
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0

print(is_power_of_two(16))  # True
print(is_power_of_two(18))  # False
```

**Finding the single number that appears once, when every other number appears twice**, using XOR — since `x ^ x == 0` and `x ^ 0 == x`, XOR-ing everything together cancels out every pair, leaving only the unpaired value:

```python
def find_single(nums):
    result = 0
    for num in nums:
        result ^= num
    return result

print(find_single([4, 1, 2, 1, 2]))  # 4
```

---

## 10.3 When Bit Manipulation Helps

Bit manipulation isn't something you reach for on every problem, but it's the right tool in a few recognizable situations:

- **Extreme performance needs** — bitwise operations are among the fastest things a CPU can do, so replacing arithmetic (`* 2`, `/ 2`, `% 2`) with shifts and masks can matter in very hot code paths (this is a minor win in most modern code, since compilers often do it automatically, but it matters in embedded/systems programming).
- **Compact storage of flags/sets** — instead of a list of booleans or a set of small integers, a single integer can store many yes/no flags as individual bits (a "bitset"), checked and updated with masks. This is common in DP problems where the "state" is which of a small number of items have been used ("bitmask DP").
- **Problems explicitly about binary representation** — counting set bits, finding missing/duplicate numbers, XOR-based puzzles, and similar problems are direct applications of the tricks above.
- **Working with hardware, protocols, or file formats** — flags, permissions (like Unix file permissions), and binary network/file formats are frequently defined bit-by-bit.

As a rule of thumb: if a problem statement mentions binary representation directly, involves a small fixed number of items where "which subset is used" matters, or the brute-force solution uses `% 2`, `* 2`, `/ 2`, or repeated even/odd checks, it's worth considering whether a bitwise approach simplifies or speeds up the solution.

[Previous](./[9]-String-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md)
