[Previous](./[10]-Logic-and-Bitwise.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[12]-Control-Flow.md)

*Core Instructions*

# Lesson 11 - The Flags Register & Comparisons (`cmp`, `test`)

Programs make decisions by comparing values, and the CPU records the outcome of every arithmetic or logic instruction in the **flags register**. This lesson explains the flags that matter, shows how `cmp` and `test` produce them, and lists which instructions modify them.

---

## 11.1 Zero, Carry, Sign, and Overflow Flags

The four flags you will use most:

| Flag | Name | Set (1) when... |
|------|------|-----------------|
| **ZF** | Zero | The result is **zero** |
| **CF** | Carry | An **unsigned** operation carried out of (or borrowed into) the top bit |
| **SF** | Sign | The top bit of the result is 1 (the result is negative when viewed as signed) |
| **OF** | Overflow | A **signed** operation produced a result that does not fit |

Two more are used less often:

| Flag | Name | Meaning |
|------|------|---------|
| **PF** | Parity | The low 8 bits of the result contain an even number of 1s |
| **AF** | Auxiliary carry | A carry from bit 3 to bit 4 (for BCD arithmetic) |

### Flag examples on 8-bit values

| Code | Result | ZF | CF | SF | OF |
|------|--------|----|----|----|----|
| `mov al, 5` / `sub al, 5` | 0 | **1** | 0 | 0 | 0 |
| `mov al, 3` / `sub al, 5` | 254 (-2) | 0 | **1** | **1** | 0 |
| `mov al, 255` / `add al, 1` | 0 | **1** | **1** | 0 | 0 |
| `mov al, 127` / `add al, 1` | 128 (-128) | 0 | 0 | **1** | **1** |
| `mov al, 100` / `add al, 20` | 120 | 0 | 0 | 0 | 0 |

In the second row, `3 - 5` borrows, so `CF = 1` for unsigned values, and the result `0xFE` has its top bit set, so `SF = 1` for signed values.

### Reading the flags

In GDB, `info registers eflags` shows which flags are set. In code, you normally do not read the flags directly. You use a **conditional jump** (Lesson 12), a **conditional set** (`setcc`), or a **conditional move** (`cmovcc`).

```nasm
sete  al                ; al = 1 if ZF = 1, else 0
cmovl rax, rbx          ; rax = rbx only if the last comparison was "less" (signed)
```

---

## 11.2 `cmp` and `test`

### `cmp`

`cmp a, b` **subtracts `b` from `a` and throws the result away**, keeping only the flags. Neither operand changes.

```nasm
cmp rax, rbx            ; flags as for rax - rbx
cmp eax, 10             ; compare with an immediate
cmp dword [count], 0    ; compare memory with an immediate (size required)
cmp al, 'A'             ; compare with a character
```

After `cmp a, b`, the flags tell you everything about the relationship between `a` and `b`:

| Relationship | Unsigned condition | Signed condition |
|--------------|--------------------|------------------|
| `a == b` | `ZF = 1` | `ZF = 1` |
| `a != b` | `ZF = 0` | `ZF = 0` |
| `a < b` | `CF = 1` | `SF != OF` |
| `a >= b` | `CF = 0` | `SF == OF` |
| `a > b` | `CF = 0` and `ZF = 0` | `ZF = 0` and `SF == OF` |
| `a <= b` | `CF = 1` or `ZF = 1` | `ZF = 1` or `SF != OF` |

The jump instructions in Lesson 12 are simply names for these flag combinations.

Why do signed and unsigned differ? Take the 8-bit values `0xFF` and `0x01`. Unsigned, `255 > 1`. Signed, `-1 < 1`. The same `cmp al, bl` is used for both, and you choose the interpretation afterwards by picking `ja` (unsigned) or `jg` (signed).

### `test`

`test a, b` performs a **bitwise AND** and discards the result, setting `ZF`, `SF`, and `PF` (and clearing `CF` and `OF`).

```nasm
test rax, rax           ; ZF = 1 if rax == 0; SF = 1 if rax < 0
test al, 0b00000100     ; ZF = 1 if bit 2 is clear
test eax, 1             ; ZF = 1 if eax is even
```

`test rax, rax` is the standard way to compare a register against zero. It is shorter than `cmp rax, 0` and just as fast.

### A complete example: using `setcc`

```nasm
; file: compare.asm
global _start

section .text
_start:
    mov rax, 10
    mov rbx, 20
    cmp rax, rbx            ; 10 - 20 -> CF = 1, SF = 1, OF = 0, ZF = 0
    setb dil                ; unsigned below -> dil = 1
    setl sil                ; signed less    -> sil = 1
    add  dil, sil           ; dil = 2
    movzx edi, dil          ; zero-extend into edi
    mov eax, 60
    syscall                 ; exit status 2
```

A signed/unsigned disagreement:

```nasm
; file: signedness.asm
global _start

section .text
_start:
    mov eax, 0xFFFFFFFF     ; unsigned 4294967295, signed -1
    mov ebx, 1
    cmp eax, ebx

    seta  dil               ; unsigned above: 1 (4294967295 > 1)
    setg  sil               ; signed greater: 0 (-1 > 1 is false)
    movzx edi, dil
    movzx esi, sil
    imul  edi, edi, 10
    add   edi, esi          ; 1 * 10 + 0 = 10
    mov eax, 60
    syscall                 ; exit status 10
```

---

## 11.3 How Instructions Affect Flags

Not every instruction updates the flags, and the ones that do update different subsets. Getting this wrong causes subtle bugs, for example putting an instruction that changes flags between a `cmp` and its jump.

| Instruction(s) | Flags changed |
|----------------|---------------|
| `add`, `sub`, `cmp`, `neg` | `CF`, `OF`, `ZF`, `SF`, `PF`, `AF` (all status flags) |
| `adc`, `sbb` | All status flags (and **use** `CF` as input) |
| `inc`, `dec` | `OF`, `ZF`, `SF`, `PF`, `AF`; **`CF` is not changed** |
| `and`, `or`, `xor`, `test` | `ZF`, `SF`, `PF` set from the result; `CF` and `OF` cleared |
| `shl`, `shr`, `sar` | `CF` = last bit shifted out; `ZF`, `SF`, `PF` from the result; **no change if the count is 0** |
| `mul`, `imul` | `CF` and `OF` indicate whether the upper half is significant; the rest are undefined |
| `div`, `idiv` | All status flags are **undefined** |
| `bt`, `bts`, `btr`, `btc` | `CF` = the tested bit |
| **`mov`, `lea`, `xchg`** | **No flags changed** |
| **`not`** | **No flags changed** |
| `push`, `pop`, `call`, `ret`, `jmp`, `jcc` | No flags changed (except `popfq`) |
| `cld`, `std` | Only `DF` |
| `clc`, `stc`, `cmc` | Only `CF` (clear, set, complement) |

Practical consequences:

**1. Flags survive `mov` and `lea`.** You can load values between a `cmp` and its jump:

```nasm
cmp rax, 10
mov rbx, 5              ; does not disturb the flags
jl  smaller
```

**2. Flags do not survive arithmetic.** This is a bug:

```nasm
cmp rax, 10
add rbx, 1              ; overwrites the flags from cmp!
jl  smaller             ; now tests the result of add, not the comparison
```

**3. Arithmetic already sets flags**, so you often do not need a `cmp` at all:

```nasm
dec rcx
jnz again               ; ZF came from dec itself

sub rax, rbx
js  negative            ; SF came from sub
```

**4. `inc`/`dec` preserve `CF`**, which is needed by multi-precision loops that use `adc`.

**5. After `div` or `idiv` do not trust any flags.** Compare the quotient or remainder explicitly with `test`.

### Manipulating flags directly

```nasm
clc                     ; CF = 0
stc                     ; CF = 1
cmc                     ; CF = !CF
cld                     ; DF = 0 (string ops go forward)
std                     ; DF = 1 (string ops go backward)
pushfq                  ; push rflags onto the stack
popfq                   ; pop rflags from the stack
lahf / sahf             ; copy flags to and from ah (low byte of flags only)
```

### Conditional moves and sets

These let you use flags without a jump, which is often faster when the condition is unpredictable (Lesson 37):

| Instruction | Condition | Instruction | Condition |
|-------------|-----------|-------------|-----------|
| `sete` / `cmove` | equal (`ZF=1`) | `setne` / `cmovne` | not equal |
| `setl` / `cmovl` | signed less | `setg` / `cmovg` | signed greater |
| `setb` / `cmovb` | unsigned below | `seta` / `cmova` | unsigned above |
| `sets` / `cmovs` | negative (`SF=1`) | `seto` / `cmovo` | overflow |

```nasm
; rax = max(rax, rbx), signed, no branch
cmp   rax, rbx
cmovl rax, rbx          ; if rax < rbx then rax = rbx
```

`setcc` writes only one byte, so zero the register first or use `movzx` if you need a full-width result. `cmovcc` does not accept an immediate operand.

---

[Previous](./[10]-Logic-and-Bitwise.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[12]-Control-Flow.md)
