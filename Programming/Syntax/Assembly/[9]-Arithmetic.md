[Previous](./[8]-Data-and-Addressing.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[10]-Logic-and-Bitwise.md)

*Core Instructions*

# Lesson 9 - Arithmetic Instructions (`add`, `sub`, `mul`, `div`, `inc`)

Arithmetic is the heart of computation. This lesson covers addition, subtraction, multiplication, and division for both signed and unsigned numbers, and explains how the carry and overflow flags report when a result does not fit.

---

## 9.1 Addition and Subtraction

| Instruction | Operation | Notes |
|-------------|-----------|-------|
| `add dest, src` | `dest = dest + src` | Sets `CF`, `OF`, `ZF`, `SF` |
| `sub dest, src` | `dest = dest - src` | Sets `CF` (borrow), `OF`, `ZF`, `SF` |
| `adc dest, src` | `dest = dest + src + CF` | Add with carry |
| `sbb dest, src` | `dest = dest - src - CF` | Subtract with borrow |

```nasm
mov rax, 100
add rax, 50             ; rax = 150
sub rax, 30             ; rax = 120
add rax, rbx            ; register + register
add [total], eax        ; memory destination (sizes must agree)
sub eax, [price]        ; memory source
```

`add` and `sub` do **not** care whether you treat the values as signed or unsigned. The bits of the result are identical; only the flags you check afterwards differ (Section 9.4).

### Multi-precision arithmetic with `adc`

A 64-bit register holds at most 2⁶⁴ − 1. To add larger numbers, split them into 64-bit pieces. Add the low halves with `add` and the high halves with `adc`, which includes the carry from the first addition:

```nasm
; file: add128.asm
global _start

section .text
_start:
    ; compute 0xFFFFFFFFFFFFFFFF + 1 as a 128-bit addition
    ; first number: rdx:rax, second number: rcx:rbx  (high:low)
    mov rax, 0xFFFFFFFFFFFFFFFF     ; low  half of first
    xor edx, edx                    ; high half of first = 0
    mov rbx, 1                      ; low  half of second
    xor ecx, ecx                    ; high half of second = 0

    add rax, rbx                    ; low halves: result 0, carry out
    adc rdx, rcx                    ; high halves plus carry: rdx = 1

    mov rdi, rdx                    ; exit with the high half (1)
    mov rax, 60
    syscall
```

Note that `xor` clears `CF`, so zero the high halves **before** the `add`, not between the `add` and the `adc`.

---

## 9.2 Multiplication and Division

### Unsigned multiplication: `mul`

`mul src` has a single operand. The other multiplicand is implicitly `al`, `ax`, `eax`, or `rax` (depending on the operand size), and the double-width result is placed in a register pair:

| Operand size | Multiplies | Result in |
|--------------|-----------|-----------|
| 8-bit | `al * src` | `ax` |
| 16-bit | `ax * src` | `dx:ax` |
| 32-bit | `eax * src` | `edx:eax` |
| 64-bit | `rax * src` | `rdx:rax` (high:low) |

```nasm
mov rax, 6
mov rbx, 7
mul rbx                 ; rdx:rax = 42, so rax = 42 and rdx = 0
```

`mul` always overwrites `rdx` (in the 32/64-bit forms), so do not keep anything important there.

### Signed multiplication: `imul`

`imul` has three forms and is the one you will use most. The two- and three-operand forms are not double-width: they truncate to the destination size and set `CF`/`OF` if the result did not fit.

```nasm
imul rbx                ; rdx:rax = rax * rbx   (one operand, double-width, signed)
imul rax, rbx           ; rax = rax * rbx
imul rax, [x]           ; rax = rax * [x]
imul rax, rbx, 10       ; rax = rbx * 10        (three operands, immediate)
```

For multiplying by a constant, the three-operand form is simplest and works for both signed and unsigned values, because the low half of the product is identical.

### Unsigned division: `div`

`div src` divides the **double-width** value in `rdx:rax` by `src`. The quotient goes to `rax` and the remainder to `rdx`.

| Operand size | Dividend | Quotient | Remainder |
|--------------|----------|----------|-----------|
| 8-bit | `ax` | `al` | `ah` |
| 16-bit | `dx:ax` | `ax` | `dx` |
| 32-bit | `edx:eax` | `eax` | `edx` |
| 64-bit | `rdx:rax` | `rax` | `rdx` |

> **Always set up `rdx` first.** If you forget to clear `rdx` before `div`, the CPU divides a huge garbage number and usually raises a **divide error**, which Linux reports as `Floating point exception`.

```nasm
mov rax, 142
xor edx, edx            ; clear the upper half of the dividend
mov rcx, 5
div rcx                 ; rax = 28 (quotient), rdx = 2 (remainder)
```

### Signed division: `idiv`

`idiv` works the same way but treats everything as signed. Before it, **sign-extend** `rax` into `rdx` with `cqo` (for 32-bit use `cdq`, for 16-bit use `cwd`):

```nasm
mov rax, -17
cqo                     ; rdx = all copies of rax's sign bit (here all 1s)
mov rcx, 5
idiv rcx                ; rax = -3 (quotient), rdx = -2 (remainder)
```

Division **truncates toward zero**, and the remainder takes the sign of the dividend, so `-17 = 5 * (-3) + (-2)`.

### Division errors

The CPU raises a divide-error exception (`#DE`) if the divisor is **zero**, or if the quotient does not fit in the destination register. Linux turns it into the signal `SIGFPE`.

### A complete example

```nasm
; file: muldiv.asm
global _start

section .text
_start:
    mov rax, 6
    mov rbx, 7
    mul rbx                 ; rax = 42
    add rax, 100            ; rax = 142

    xor edx, edx            ; clear dividend high half
    mov rcx, 5
    div rcx                 ; rax = 28, rdx = 2

    mov rdi, rax
    add rdi, rdx            ; 28 + 2 = 30
    mov rax, 60
    syscall                 ; exit status 30
```

### Multiplying and dividing by powers of two

Shifts are faster than `mul` and `div` for powers of two (Lesson 10):

```nasm
shl rax, 3              ; rax = rax * 8
shr rax, 2              ; rax = rax / 4  (unsigned)
sar rax, 2              ; rax = rax / 4  (signed, rounds toward -infinity)
```

---

## 9.3 Increment, Decrement, and Negation

| Instruction | Operation | Notes |
|-------------|-----------|-------|
| `inc dest` | `dest = dest + 1` | Does **not** change `CF` |
| `dec dest` | `dest = dest - 1` | Does **not** change `CF` |
| `neg dest` | `dest = -dest` | Two's complement negation; `CF = 1` unless the operand was zero |

```nasm
inc rcx                 ; counter++
dec rcx                 ; counter--
inc dword [count]       ; memory operand needs a size
neg rax                 ; rax = -rax
```

`inc` and `dec` leave `CF` untouched, which makes them useful in multi-word arithmetic loops where `CF` carries information between iterations.

`neg` of the most negative value (`-128` for a byte, `-2³¹` for a dword) gives the same value back and sets `OF`, because the positive counterpart does not fit.

Related instructions:

```nasm
cmp rax, rbx            ; computes rax - rbx, sets flags, discards result (Lesson 11)
xadd [x], eax           ; exchange and add: eax = old [x]; [x] += old eax
```

---

## 9.4 Overflow and Carry

A result **overflows** when it does not fit in the destination size. The CPU cannot know if you are working with signed or unsigned values, so it reports **both** possibilities at once with two different flags:

| Flag | Meaning | Check it when your numbers are... |
|------|---------|----------------------------------|
| **CF** (Carry) | Unsigned overflow: a carry out of the top bit (or a borrow for `sub`) | **unsigned** |
| **OF** (Overflow) | Signed overflow: the result's sign is wrong | **signed** |

### Examples with 8-bit registers

```nasm
mov al, 255
add al, 1               ; al = 0     CF = 1 (255 + 1 doesn't fit unsigned)   OF = 0 (255 is -1 signed; -1 + 1 = 0 is fine)

mov al, 127
add al, 1               ; al = 0x80  CF = 0 (128 fits unsigned)              OF = 1 (127 + 1 = 128 doesn't fit signed; became -128)

mov al, 200
add al, 100             ; al = 44    CF = 1 (300 > 255)                       OF = 0 (-56 + 100 = 44 is fine signed)

mov al, 5
sub al, 10              ; al = 251   CF = 1 (borrow: 5 < 10 unsigned)        OF = 0
```

A signed overflow occurs only when adding two numbers of the **same sign** gives a result of the **opposite sign** (positive + positive = negative, or negative + negative = positive).

### Using the flags

You act on these flags with conditional jumps (Lesson 12):

```nasm
add rax, rbx
jc  unsigned_overflow   ; jump if carry set
jo  signed_overflow     ; jump if overflow set
```

Many languages hide overflow. In assembly you must check it yourself if it matters. Unchecked overflow is a classic source of security bugs (integer overflow followed by a small allocation and a large copy).

### Detecting overflow in `imul` and `mul`

- `mul` sets `CF = OF = 1` if the upper half of the result (`rdx`) is not zero.
- `imul` (two- and three-operand forms) sets `CF = OF = 1` if the full signed result does not fit in the destination.

---

[Previous](./[8]-Data-and-Addressing.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[10]-Logic-and-Bitwise.md)
