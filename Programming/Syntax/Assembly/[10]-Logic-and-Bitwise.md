[Previous](./[9]-Arithmetic.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[11]-Flags-and-Comparisons.md)

*Core Instructions*

# Lesson 10 - Logic & Bitwise Instructions (`and`, `or`, `xor`, `shl`, `shr`)

Bitwise instructions work on the individual bits of a value. They are used for flags and masks, hardware registers, packing data, fast arithmetic, and cryptography. This lesson covers the logic operations, shifts and rotates, and the common patterns built from them.

---

## 10.1 AND, OR, XOR, NOT

Each of these operates on every bit position independently.

| Instruction | Operation | Result bit is 1 when... |
|-------------|-----------|-------------------------|
| `and dest, src` | bitwise AND | both bits are 1 |
| `or dest, src` | bitwise OR | at least one bit is 1 |
| `xor dest, src` | bitwise exclusive OR | the bits are **different** |
| `not dest` | bitwise inversion | the bit was 0 (flips every bit) |
| `test a, b` | AND, result discarded | (only sets flags, Lesson 11) |

Truth table:

| A | B | `A and B` | `A or B` | `A xor B` |
|---|---|-----------|----------|-----------|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 1 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 |

An example on 8 bits:

```
  1011 0110      (0xB6)
& 0000 1111      (0x0F)
-----------
  0000 0110      (0x06)   AND keeps only the bits where the mask has 1
```

```nasm
mov al, 0b10110110
and al, 0b00001111      ; al = 0b00000110
or  al, 0b01000000      ; al = 0b01000110
xor al, 0b00000110      ; al = 0b01000000
not al                  ; al = 0b10111111
```

`and`, `or`, and `xor` set `ZF`, `SF`, and `PF` according to the result and **clear `CF` and `OF`**. `not` does not affect any flags.

### The two famous uses of `xor`

```nasm
xor eax, eax            ; set eax (and all of rax) to 0: shorter than mov eax, 0
xor al, 0x20            ; flip bit 5 (toggles letter case)
```

`xor reg, reg` is the idiomatic way to zero a register: it is 2 bytes (versus 5 for `mov eax, 0`), and CPUs recognise it as a zeroing idiom. Note that it changes the flags, while `mov` does not.

---

## 10.2 Shifts and Rotates

A **shift** moves all bits left or right by a number of positions. The count is either an immediate or the register `cl`.

| Instruction | Name | Behaviour |
|-------------|------|-----------|
| `shl dest, n` (`sal`) | Shift left | Fills vacated bits on the right with 0. Multiplies by 2ⁿ |
| `shr dest, n` | Shift right (logical) | Fills with 0 on the left. Unsigned division by 2ⁿ |
| `sar dest, n` | Shift arithmetic right | Fills with a **copy of the sign bit**. Signed division by 2ⁿ |
| `rol dest, n` | Rotate left | Bits that fall off the left reappear on the right |
| `ror dest, n` | Rotate right | Bits that fall off the right reappear on the left |
| `rcl`, `rcr` | Rotate through carry | Treats `CF` as an extra bit |

The last bit shifted out ends up in the **carry flag**.

```
shl al, 1:     CF ◄── [ 1 0 1 1 0 0 1 1 ] ◄── 0
shr al, 1:      0 ──► [ 1 0 1 1 0 0 1 1 ] ──► CF
sar al, 1:  (sign)──► [ 1 0 1 1 0 0 1 1 ] ──► CF       (top bit is replicated)
rol al, 1:  ┌─ CF ◄── [ 1 0 1 1 0 0 1 1 ] ◄─┐
            └──────────────────────────────┘
```

Examples:

```nasm
mov eax, 5              ; 0000 0101
shl eax, 3              ; 0010 1000 = 40       (5 * 8)

mov eax, 40
shr eax, 2              ; 0000 1010 = 10       (40 / 4)

mov eax, -16            ; 0xFFFFFFF0
sar eax, 2              ; 0xFFFFFFFC = -4      (sign preserved)
shr eax, 2              ; (instead) would give 0x3FFFFFFC: wrong for a negative number

mov cl, 4
shl rax, cl             ; variable count must be in cl
```

`sar` rounds toward negative infinity: `-5 sar 1` is `-3`, while `idiv` by 2 gives `-2`. This difference matters when compilers turn signed division into shifts (Lesson 24).

Rotations are used in hashing and encryption (SHA, ChaCha) because no bits are lost.

```nasm
mov eax, 0x80000001
rol eax, 1              ; eax = 0x00000003  (the top bit wrapped around to bit 0)
```

> **Count behaviour:** shift counts are masked (to 5 bits for 32-bit operands and 6 bits for 64-bit operands), so shifting a 32-bit value by 33 is the same as shifting by 1. A count of 0 leaves the flags unchanged.

---

## 10.3 Masking and Setting Bits

A **mask** is a constant with 1s at the bit positions you want to affect. Bit numbering starts at 0 on the right.

| Goal | Operation | Example (bit 3 of `al`) |
|------|-----------|-------------------------|
| **Clear** a bit | `and` with a mask that has 0 there | `and al, 0b11110111` |
| **Set** a bit | `or` with a mask that has 1 there | `or al, 0b00001000` |
| **Toggle** a bit | `xor` with a mask that has 1 there | `xor al, 0b00001000` |
| **Test** a bit | `test` with a mask, then check `ZF` | `test al, 0b00001000` |
| **Extract** a field | shift right, then `and` | `shr al, 4` / `and al, 0b11` |

You can write the mask as `1 << n` (NASM evaluates this at assembly time):

```nasm
or   eax, 1 << 5        ; set bit 5
and  eax, ~(1 << 5)     ; clear bit 5
xor  eax, 1 << 5        ; toggle bit 5
test eax, 1 << 5        ; is bit 5 set?  ZF = 1 if it is clear
jnz  bit_is_set
```

The `bt` family does the same on a single bit and puts the old value in `CF`:

| Instruction | Action |
|-------------|--------|
| `bt eax, 5` | Copy bit 5 into `CF` |
| `bts eax, 5` | Copy bit 5 into `CF`, then **set** it |
| `btr eax, 5` | Copy bit 5 into `CF`, then **clear** it |
| `btc eax, 5` | Copy bit 5 into `CF`, then **toggle** it |

### Packing and unpacking fields

Hardware registers and file formats pack several small values in one number. For example, a 16-bit RGB565 colour uses 5 bits red, 6 bits green, 5 bits blue:

```
 15      11 10       5 4       0
┌──────────┬──────────┬─────────┐
│  red (5) │ green (6)│ blue (5)│
└──────────┴──────────┴─────────┘
```

```nasm
; file: unpack.asm
global _start

section .text
_start:
    mov eax, 0xF81F             ; red = 31, green = 0, blue = 31
    mov ebx, eax
    shr ebx, 11                 ; red = bits 15..11
    and ebx, 0b11111            ; keep 5 bits -> 31
    mov edi, ebx
    mov eax, 60
    syscall                     ; exit status 31
```

### A worked example

```nasm
; file: bits.asm
global _start

section .text
_start:
    mov eax, 0b10110110     ; 182
    and eax, 0x0F           ; keep low nibble:   0b0110  = 6
    or  eax, 0x10           ; set bit 4:         0b10110 = 22
    xor eax, 0x01           ; toggle bit 0:      0b10111 = 23
    shl eax, 1              ; multiply by 2:     46
    mov edi, eax
    mov eax, 60
    syscall                 ; exit status 46
```

---

## 10.4 Common Bit Tricks

These patterns appear constantly in compiler output and low-level code.

**Zero a register:**
```nasm
xor eax, eax
```

**Multiply and divide by powers of two:**
```nasm
shl rax, 4              ; rax *= 16
shr rax, 4              ; rax /= 16 (unsigned)
```

**Test for even or odd:** the lowest bit is 1 for odd numbers.
```nasm
test al, 1
jz   is_even
```

**Change letter case** (ASCII letters differ by bit 5, `0x20`):
```nasm
or  al, 0x20            ; 'A' (0x41) -> 'a' (0x61)   (only for letters!)
and al, 0xDF            ; 'a' (0x61) -> 'A' (0x41)   (0xDF = ~0x20)
xor al, 0x20            ; toggle case
```

**Is a number a power of two?** A power of two has exactly one bit set, so `x & (x - 1)` is zero. (Zero also passes, so check it separately.)
```nasm
lea  rbx, [rax - 1]
test rax, rbx
jz   power_of_two       ; ZF set when rax & (rax-1) == 0
```

**Clear the lowest set bit:**
```nasm
lea rbx, [rax - 1]
and rax, rbx            ; x & (x - 1)
```

**Isolate the lowest set bit:**
```nasm
mov rbx, rax
neg rbx
and rax, rbx            ; x & -x
```

**Swap two registers without a temporary** (a curiosity; `xchg` or three `mov`s are usually better):
```nasm
xor rax, rbx
xor rbx, rax
xor rax, rbx
```

**Count the set bits** (population count):
```nasm
popcnt rax, rbx         ; rax = number of 1 bits in rbx  (needs a CPU with POPCNT)
```

**Find the highest or lowest set bit:**
```nasm
bsf rax, rbx            ; index of lowest set bit (ZF=1 if rbx was 0, rax undefined)
bsr rax, rbx            ; index of highest set bit
```

**Round up to a multiple of 16** (used for stack alignment, Lesson 17):
```nasm
add rax, 15
and rax, -16            ; -16 is 0xFFFF...F0
```

**Absolute value without branching:**
```nasm
mov rbx, rax
sar rbx, 63             ; rbx = 0 if rax >= 0, else all ones
xor rax, rbx
sub rax, rbx            ; rax = |rax|
```

---

[Previous](./[9]-Arithmetic.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[11]-Flags-and-Comparisons.md)
