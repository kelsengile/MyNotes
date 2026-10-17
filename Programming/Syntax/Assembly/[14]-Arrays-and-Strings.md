[Previous](./[13]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[15]-The-Stack.md)

*Core Instructions*

# Lesson 14 - Arrays & Strings in Assembly (`rep`, `movs`, `scas`)

Arrays and strings are both just sequences of elements in memory. This lesson shows how to index arrays, how C-style strings work, and how the x86 string instructions with the `rep` prefix can copy, fill, search, and compare memory with a single instruction.

---

## 14.1 Indexing Arrays

An array is a block of equal-sized elements stored one after another. The address of element `i` is:

```
address = base + i * element_size
```

The scaled-index addressing mode (Lesson 8) does the multiplication for free when the element size is 1, 2, 4, or 8:

| Element type | Size | Access to element `i` (index in `rcx`) |
|--------------|------|----------------------------------------|
| `db` | 1 | `[array + rcx]` |
| `dw` | 2 | `[array + rcx*2]` |
| `dd` | 4 | `[array + rcx*4]` |
| `dq` | 8 | `[array + rcx*8]` |

Remember that indexes start at **0**, and the CPU does no bounds checking.

### Summing an array

```nasm
; file: array_sum.asm
global _start

section .data
numbers dd 10, 20, 30, 40
COUNT   equ ($ - numbers) / 4       ; number of dwords, computed at assembly time

section .text
_start:
    xor  eax, eax                   ; sum = 0
    xor  ecx, ecx                   ; i = 0
.loop:
    cmp  ecx, COUNT
    jae  .done
    add  eax, [numbers + rcx*4]     ; sum += numbers[i]
    inc  ecx
    jmp  .loop
.done:
    mov  edi, eax
    mov  eax, 60
    syscall                         ; exit status 100
```

### Pointer-style traversal

Instead of an index, step a pointer through the array. This is often shorter and is what the string instructions do.

```nasm
    lea  rsi, [numbers]             ; pointer to the first element
    lea  rdx, [numbers + COUNT*4]   ; pointer one past the end
    xor  eax, eax
.loop:
    add  eax, [rsi]
    add  rsi, 4                     ; advance by one element (4 bytes)
    cmp  rsi, rdx
    jb   .loop
```

### Two-dimensional arrays

A `rows × cols` matrix stored row by row has the element `[r][c]` at `base + (r * cols + c) * size`:

```nasm
    ; matrix of 3 rows x 4 columns of dwords, element [r=1][c=2]
    mov  rax, 1                     ; r
    imul rax, rax, 4                ; r * cols
    add  rax, 2                     ; + c
    mov  edx, [matrix + rax*4]
```

---

## 14.2 Null-Terminated Strings

A **string** is an array of bytes holding character codes. C and the Linux kernel use **null-terminated** strings: the end is marked by a byte with value `0`, so the length is not stored.

```nasm
section .data
name    db "Assembly", 0            ; 9 bytes: 8 characters + the NUL terminator
```

```
Address:  +0   +1   +2   +3   +4   +5   +6   +7   +8
Bytes:    'A'  's'  's'  'e'  'm'  'b'  'l'  'y'  0
Hex:      41   73   73   65   6D   62   6C   79   00
```

Alternatively, you can store the **length separately**, which is how `write` works (it takes a pointer and a byte count). NASM can compute it for you:

```nasm
msg     db "Hello", 10
msg_len equ $ - msg
```

### Computing a string's length with a loop

```nasm
; file: strlen_loop.asm
global _start

section .data
name db "Assembly", 0

section .text
_start:
    lea  rdi, [name]            ; pointer to the string
    xor  eax, eax               ; length = 0
.scan:
    cmp  byte [rdi + rax], 0    ; reached the terminator?
    je   .done
    inc  rax
    jmp  .scan
.done:
    mov  rdi, rax               ; rax = length
    mov  eax, 60
    syscall                     ; exit status 8
```

### Processing characters

Bytes are loaded with `mov al, [rsi]` or, to avoid partial-register issues, with `movzx eax, byte [rsi]`. A loop that converts a string to upper case:

```nasm
; file: upper.asm
global _start

section .data
text    db "hello, world", 10
len     equ $ - text

section .text
_start:
    lea  rsi, [text]
    mov  ecx, len
.next:
    mov  al, [rsi]
    cmp  al, 'a'
    jb   .skip                  ; below 'a' -> not a lower-case letter
    cmp  al, 'z'
    ja   .skip                  ; above 'z' -> not a lower-case letter
    and  al, 0xDF               ; clear bit 5 -> upper case
    mov  [rsi], al              ; store it back (.data is writable)
.skip:
    inc  rsi
    dec  ecx
    jnz  .next

    mov  eax, 1                 ; write(1, text, len)
    mov  edi, 1
    lea  rsi, [text]
    mov  edx, len
    syscall

    mov  eax, 60
    xor  edi, edi
    syscall
```

Output:

```
HELLO, WORLD
```

---

## 14.3 String Instructions and the `rep` Prefix

x86 has a family of **string instructions** that each process one element and then advance pointer registers automatically. Despite the name they work on any block of memory.

They use fixed registers:

| Register | Role |
|----------|------|
| `rsi` | **Source** pointer |
| `rdi` | **Destination** pointer |
| `rcx` | **Count** (for `rep`) |
| `al` / `ax` / `eax` / `rax` | The value to store, load, or search for |

### The instructions

| Instruction | Operation | Pointer changes |
|-------------|-----------|-----------------|
| `movsb` / `movsw` / `movsd` / `movsq` | `[rdi] = [rsi]` (copy 1/2/4/8 bytes) | `rsi`, `rdi` |
| `stosb` / `stosw` / `stosd` / `stosq` | `[rdi] = al/ax/eax/rax` (store) | `rdi` |
| `lodsb` / `lodsw` / `lodsd` / `lodsq` | `al/ax/eax/rax = [rsi]` (load) | `rsi` |
| `scasb` / `scasw` / `scasd` / `scasq` | compare `al/ax/eax/rax` with `[rdi]` (sets flags) | `rdi` |
| `cmpsb` / `cmpsw` / `cmpsd` / `cmpsq` | compare `[rsi]` with `[rdi]` (sets flags) | `rsi`, `rdi` |

After each element the pointers move by the element size: **forward** (increment) if the **direction flag** `DF = 0`, **backward** if `DF = 1`.

```nasm
cld                     ; DF = 0: go forward (the normal state; always set it explicitly)
std                     ; DF = 1: go backward
```

The Linux ABI guarantees `DF = 0` on entry to functions, and you must restore it to 0 before returning.

### The `rep` prefixes

A prefix repeats the instruction:

| Prefix | Behaviour | Used with |
|--------|-----------|-----------|
| `rep` | Repeat `rcx` times | `movs`, `stos`, `lods` |
| `repe` / `repz` | Repeat while `rcx != 0` **and** `ZF = 1` (stop at first difference) | `cmps`, `scas` |
| `repne` / `repnz` | Repeat while `rcx != 0` **and** `ZF = 0` (stop at first match) | `cmps`, `scas` |

If `rcx` is 0 the instruction does nothing.

### Copying memory: `rep movsb`

```nasm
    lea  rsi, [source]
    lea  rdi, [dest]
    mov  ecx, 12                ; number of bytes
    cld
    rep  movsb                  ; copy 12 bytes from [rsi] to [rdi]
```

Use `rep movsq` with `rcx = bytes / 8` to copy eight bytes at a time.

### Filling memory: `rep stosb`

```nasm
    lea  rdi, [buffer]
    mov  al, '-'                ; value to store
    mov  ecx, 20
    cld
    rep  stosb                  ; fill 20 bytes with '-'
```

### Searching: `repne scasb`

`scasb` compares `al` with `[rdi]` (like `cmp al, [rdi]`). With `repne` it keeps scanning until a byte *equals* `al` or `rcx` runs out.

### Comparing: `repe cmpsb`

`cmpsb` compares `[rsi]` with `[rdi]`. With `repe` it keeps going while the bytes are equal.

### Single-step use

The non-repeated forms are useful inside manual loops, for example `lodsb` loads a byte and advances `rsi` in one instruction:

```nasm
.next:
    lodsb                       ; al = [rsi]; rsi++
    test al, al
    jz   .done
    ; ... process al ...
    jmp  .next
```

### Performance note

`rep movsb` and `rep stosb` are specifically optimised in modern CPUs (the "fast string operations" feature) and are competitive for medium and large copies. `repne scasb`, `repe cmpsb`, and `rep lodsb` are slow, and libraries use SIMD instead (Lesson 27).

---

## 14.4 Computing String Length and Copying

### `strlen` with `repne scasb`

Scan for the NUL byte, starting with the largest possible count:

```nasm
; file: strlen_scas.asm
global _start

section .data
name db "Assembly", 0

section .text
_start:
    lea  rdi, [name]            ; where to search
    xor  eax, eax               ; al = 0: the byte we look for
    mov  rcx, -1                ; count = 0xFFFFFFFFFFFFFFFF ("unlimited")
    cld
    repne scasb                 ; scan until [rdi] == 0
    not  rcx                    ; rcx = bytes scanned (including the NUL)
    dec  rcx                    ; subtract the NUL: rcx = length

    mov  rdi, rcx
    mov  eax, 60
    syscall                     ; exit status 8
```

How the arithmetic works: `rcx` starts at -1. Each scanned byte subtracts 1, so after scanning `n + 1` bytes (the `n` characters and the NUL) it holds `-1 - (n + 1) = -(n + 2)`. Then `not rcx` gives `n + 1`, and `dec rcx` gives `n`.

### Copying a string and printing it

```nasm
; file: strcpy.asm
global _start

section .data
source  db "Copy me!", 10, 0       ; includes newline and NUL
SRC_LEN equ $ - source

section .bss
dest    resb 32

section .text
_start:
    lea  rsi, [source]
    lea  rdi, [dest]
    mov  ecx, SRC_LEN
    cld
    rep  movsb                      ; copy including the terminator

    mov  eax, 1                     ; write(1, dest, SRC_LEN - 1)
    mov  edi, 1
    lea  rsi, [dest]
    mov  edx, SRC_LEN - 1           ; leave out the NUL
    syscall

    mov  eax, 60
    xor  edi, edi
    syscall
```

Output:

```
Copy me!
```

### Copying up to the terminator

When the length is not known in advance, copy one byte at a time and stop after the NUL:

```nasm
; strcpy(rdi = destination, rsi = source)
.copy:
    lodsb                           ; al = *rsi++
    stosb                           ; *rdi++ = al
    test al, al
    jnz  .copy                      ; stop after copying the NUL
```

### Comparing two strings of known length

```nasm
; file: compare_strings.asm
global _start

section .data
a   db "apple"
b   db "apple"
LEN equ 5

section .text
_start:
    lea  rsi, [a]
    lea  rdi, [b]
    mov  ecx, LEN
    cld
    repe cmpsb                      ; stops at the first difference or when rcx = 0
    sete dil                        ; ZF = 1 only if the last compared pair was equal
    movzx edi, dil                  ; 1 = equal, 0 = different
    mov  eax, 60
    syscall                         ; exit status 1
```

If the strings differ, `repe cmpsb` stops with `ZF = 0` (and `rcx` and `rsi`/`rdi` pointing just past the first mismatch). Note that `rcx` may be 0 even when the final pair differed; the flags are the reliable result.

### Filling a buffer and printing it

```nasm
; file: fill.asm
global _start

section .bss
buf resb 16

section .text
_start:
    lea  rdi, [buf]
    mov  al, '*'
    mov  ecx, 15
    cld
    rep  stosb                      ; 15 asterisks
    mov  byte [rdi], 10             ; rdi now points just past them: add a newline

    mov  eax, 1
    mov  edi, 1
    lea  rsi, [buf]
    mov  edx, 16
    syscall

    mov  eax, 60
    xor  edi, edi
    syscall
```

Output:

```
***************
```

Remember these habits whenever you use string instructions: set `DF` with `cld`, load `rsi`, `rdi`, and `rcx` first, and note that the instruction **modifies** `rsi`, `rdi`, and `rcx`, so save any values you still need.

---

[Previous](./[13]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[15]-The-Stack.md)
