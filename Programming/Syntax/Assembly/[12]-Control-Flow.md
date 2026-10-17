[Previous](./[11]-Flags-and-Comparisons.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[13]-Loops.md)

*Core Instructions*

# Lesson 12 - Control Flow: Jumps & Conditionals (`jmp`, `je`, `jne`, `jg`)

Normally the CPU executes instructions one after another. **Control flow** instructions change that order, which is how every `if`, `else`, `switch`, and loop in a high-level language is implemented. This lesson covers unconditional and conditional jumps, the difference between signed and unsigned comparisons, and how to build `if/else` structures.

---

## 12.1 Unconditional Jumps

`jmp` always transfers control to a new location by changing `rip`.

```nasm
        jmp  skip           ; jump to the label 'skip'
        mov  rax, 1         ; never executed
skip:
        mov  rax, 2
```

Forms of `jmp`:

| Form | Example | Notes |
|------|---------|-------|
| **Short** | `jmp short label` | 2 bytes; target within -128..+127 bytes |
| **Near** (relative) | `jmp label` | 5 bytes; target within ±2 GB. NASM picks the best size automatically |
| **Indirect, register** | `jmp rax` | Jump to the address held in `rax` |
| **Indirect, memory** | `jmp [table + rcx*8]` | Jump to an address read from memory (jump tables) |

The first two are encoded as a displacement relative to the next instruction (Lesson 5), so code can be moved without changing it.

### Jump tables

An indirect jump through an array of addresses is how a `switch` statement with many cases is compiled:

```nasm
; file: jumptable.asm
global _start

section .data
table dq case0, case1, case2        ; three 8-byte code addresses

section .text
_start:
    mov rsi, 1                      ; selector value
    jmp [table + rsi*8]             ; jump to table[1]

case0:
    mov rdi, 10
    jmp done
case1:
    mov rdi, 11
    jmp done
case2:
    mov rdi, 12
done:
    mov rax, 60
    syscall                         ; exit status 11
```

A real `switch` first checks that the selector is in range (`cmp rsi, 2` / `ja default`) so it never reads past the end of the table.

---

## 12.2 Conditional Jumps

A **conditional jump** (`jcc`) jumps only if a condition on the flags is true. Otherwise execution simply continues with the next instruction. They are almost always used right after `cmp`, `test`, or an arithmetic instruction.

```nasm
cmp rax, rbx
je  equal               ; jump if rax == rbx
```

### Jumps by flag

| Instruction | Name | Jumps if | Flags |
|-------------|------|----------|-------|
| `jz` / `je` | Zero / Equal | result is zero | `ZF = 1` |
| `jnz` / `jne` | Not zero / Not equal | | `ZF = 0` |
| `js` | Sign | result negative | `SF = 1` |
| `jns` | Not sign | | `SF = 0` |
| `jc` | Carry | | `CF = 1` |
| `jnc` | No carry | | `CF = 0` |
| `jo` | Overflow | | `OF = 1` |
| `jno` | No overflow | | `OF = 0` |
| `jp` / `jnp` | Parity even / odd | | `PF = 1` / `0` |
| `jrcxz` | `rcx` is zero | `rcx == 0` | (does not use flags) |

### Jumps after `cmp a, b` (unsigned)

Use these when the operands are **unsigned** numbers, addresses, or sizes.

| Instruction | Alias | Jumps if | Condition |
|-------------|-------|----------|-----------|
| `ja` | `jnbe` | a **above** b | `CF = 0` and `ZF = 0` |
| `jae` | `jnb`, `jnc` | a above or equal | `CF = 0` |
| `jb` | `jnae`, `jc` | a **below** b | `CF = 1` |
| `jbe` | `jna` | a below or equal | `CF = 1` or `ZF = 1` |

### Jumps after `cmp a, b` (signed)

Use these when the operands are **signed** numbers.

| Instruction | Alias | Jumps if | Condition |
|-------------|-------|----------|-----------|
| `jg` | `jnle` | a **greater** than b | `ZF = 0` and `SF = OF` |
| `jge` | `jnl` | a greater or equal | `SF = OF` |
| `jl` | `jnge` | a **less** than b | `SF != OF` |
| `jle` | `jng` | a less or equal | `ZF = 1` or `SF != OF` |

The mnemonic letters help: **above/below** is for unsigned, **greater/less** is for signed. `je`/`jne` are the same for both.

### Range of conditional jumps

Conditional jumps have a signed 32-bit displacement (±2 GB) when assembled by NASM, which is more than enough in practice. The short form (`jz short label`) has only 8 bits.

> **ARM64 note:** ARM64 has the same idea with different names: `cmp x0, x1` followed by `b.eq`, `b.ne`, `b.lt` (signed), `b.lo` (unsigned lower), and so on, using the `NZCV` condition flags.

---

## 12.3 Signed vs Unsigned Comparisons

The CPU compares bit patterns. Whether `0xFFFFFFFF` is "big" (4,294,967,295) or "small" (-1) depends on the **jump you choose**.

```nasm
; file: signed_unsigned.asm
global _start

section .text
_start:
    xor  edi, edi
    mov  eax, 0xFFFFFFFF        ; 4294967295 unsigned, -1 signed
    mov  ebx, 1

    cmp  eax, ebx
    jg   .signed_greater        ; signed:   -1 > 1 ?      NO
    add  edi, 1                 ; reached: signed not greater
.signed_greater:

    cmp  eax, ebx
    ja   .unsigned_above        ; unsigned: 4294967295 > 1 ? YES
    add  edi, 10                ; skipped
.unsigned_above:
    add  edi, 20                ; always reached

    mov  eax, 60
    syscall                     ; exit status 1 + 20 = 21
```

Using the wrong family is a classic bug:

- Comparing **array indices, sizes, addresses, or counters that can never be negative** with signed jumps misbehaves for values above the signed maximum.
- Comparing **values that can be negative** with unsigned jumps (`jb`, `ja`) treats negatives as huge positives.
- A famous trick uses the unsigned compare to check a range in one step: `x` is within `0..9` when `(x - 0) <=u 9`.

```nasm
; is al a decimal digit ('0'..'9')?  One unsigned compare does both bounds.
sub  al, '0'            ; map '0'..'9' to 0..9; everything else wraps to a large value
cmp  al, 9
jbe  is_digit           ; unsigned: al <= 9
```

Compilers generate exactly this pattern for range checks such as `c >= '0' && c <= '9'`.

---

## 12.4 Building if/else from Jumps

The key idea: a high-level `if` becomes a **conditional jump over the body when the condition is false**. So you test the *opposite* of what you wrote in C.

### if

```c
if (a == 5) {
    body;
}
```

```nasm
    cmp  rax, 5
    jne  .skip              ; condition is false -> skip the body
    ; body
.skip:
```

### if / else

```c
if (a < b) { x = 1; } else { x = 2; }
```

```nasm
    cmp  rax, rbx
    jge  .else              ; opposite of "<" is ">="
    mov  rcx, 1             ; then-part
    jmp  .end               ; must skip the else-part!
.else:
    mov  rcx, 2             ; else-part
.end:
```

Forgetting the `jmp .end` makes the else-part run as well.

### if / else if / else

```nasm
; file: classify.asm
global _start

section .text
_start:
    mov  rax, 57                ; the number to classify

    cmp  rax, 10
    jae  .not_small             ; if (x < 10)
    mov  rdi, 1                 ;     class 1
    jmp  .done
.not_small:
    cmp  rax, 100
    jae  .big                   ; else if (x < 100)
    mov  rdi, 2                 ;     class 2
    jmp  .done
.big:
    mov  rdi, 3                 ; else class 3
.done:
    mov  rax, 60
    syscall                     ; exit status 2
```

### Compound conditions

`&&` (and) and `||` (or) use **short-circuit** evaluation: test one part at a time and jump as soon as the answer is known.

```c
if (x > 0 && x < 100) { ... }
```

```nasm
    cmp  rax, 0
    jle  .skip              ; first part false -> whole condition false
    cmp  rax, 100
    jge  .skip              ; second part false -> whole condition false
    ; body
.skip:
```

```c
if (x == 1 || x == 2) { ... }
```

```nasm
    cmp  rax, 1
    je   .body              ; first part true -> whole condition true
    cmp  rax, 2
    jne  .skip
.body:
    ; body
.skip:
```

### Finding the larger of two numbers

```nasm
; file: max.asm
global _start

section .text
_start:
    mov  rax, 17
    mov  rbx, 42
    cmp  rax, rbx
    jge  .done              ; already the larger
    mov  rax, rbx           ; otherwise take rbx
.done:
    mov  rdi, rax
    mov  rax, 60
    syscall                 ; exit status 42
```

Branches are not free: a mispredicted branch costs many cycles (Lesson 37). For short, unpredictable choices like `max`, `cmovcc` (Lesson 11) can replace the whole branch.

---

[Previous](./[11]-Flags-and-Comparisons.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[13]-Loops.md)
