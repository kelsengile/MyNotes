[Previous](./[12]-Control-Flow.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[14]-Arrays-and-Strings.md)

*Core Instructions*

# Lesson 13 - Loops (`loop`, counters, and jump-based loops)

A **loop** repeats a block of code. Assembly has no `for` or `while`; a loop is just a **label** and a **conditional jump backward**. This lesson shows the standard loop patterns, the dedicated `loop` instruction, and how to nest loops.

---

## 13.1 Counting Loops

The simplest pattern: set a counter, run the body, change the counter, and jump back while a condition holds.

### Counting down to zero

The cheapest loop on x86. `dec` already sets `ZF`, so no `cmp` is needed:

```nasm
; file: sum_down.asm
global _start

section .text
_start:
    xor  eax, eax           ; sum = 0
    mov  ecx, 10            ; counter = 10
.again:
    add  eax, ecx           ; sum += counter     (10 + 9 + ... + 1)
    dec  ecx                ; counter--  (sets ZF when it reaches 0)
    jnz  .again             ; repeat while counter != 0

    mov  edi, eax
    mov  eax, 60
    syscall                 ; exit status 55
```

This is a **do-while** loop: the body always runs at least once, so the counter must start above 0.

### Counting up (`for` loop)

```c
for (int i = 0; i < 10; i++) { sum += i; }
```

```nasm
; file: sum_up.asm
global _start

section .text
_start:
    xor  eax, eax           ; sum = 0
    xor  ecx, ecx           ; i = 0
.for:
    cmp  ecx, 10            ; i < 10 ?
    jge  .end               ; no -> leave the loop
    add  eax, ecx           ; body: sum += i
    inc  ecx                ; i++
    jmp  .for               ; back to the test
.end:
    mov  edi, eax
    mov  eax, 60
    syscall                 ; exit status 45
```

Compilers often rotate this loop so the test is at the bottom (one jump per iteration instead of two):

```nasm
    xor  ecx, ecx
    cmp  ecx, 10            ; test once before entering
    jge  .end
.for:
    add  eax, ecx
    inc  ecx
    cmp  ecx, 10
    jl   .for               ; the only jump per iteration
.end:
```

### Choosing the loop register

Any register works, but use ones that survive the work inside the loop. `rcx`, `rdx`, `rax`, `rsi`, `rdi`, `r8`-`r11` are destroyed by a `syscall` or `call`, so across those use `rbx`, `rbp`, or `r12`-`r15` (see Lesson 17).

---

## 13.2 While and Do-While Patterns

### While loop (test first)

```c
while (x != 0) { body; }
```

```nasm
.while:
    test rax, rax           ; test at the top
    jz   .end               ; exit when condition is false
    ; body
    jmp  .while
.end:
```

A worked example: count the decimal digits of a number by repeated division by 10.

```nasm
; file: digits.asm
global _start

section .text
_start:
    mov  rax, 12345
    xor  ecx, ecx           ; digit count = 0
    mov  rbx, 10            ; divisor
.while:
    test rax, rax
    jz   .done              ; stop when the number reaches 0
    xor  edx, edx           ; clear the high half of the dividend
    div  rbx                ; rax = rax / 10
    inc  rcx
    jmp  .while
.done:
    mov  rdi, rcx
    mov  rax, 60
    syscall                 ; exit status 5
```

This loop reports 0 digits for the number 0. A do-while version would report 1.

### Do-while loop (test last)

```c
do { body; } while (x != 0);
```

```nasm
.do:
    ; body
    test rax, rax
    jnz  .do                ; repeat while the condition holds
```

### Infinite loops and `break` / `continue`

```nasm
.forever:
    ; body
    cmp  al, 'q'
    je   .break             ; break: jump past the loop
    cmp  al, ' '
    je   .forever           ; continue: jump back to the top
    ; more body
    jmp  .forever
.break:
```

`break` and `continue` are simply jumps to the label after the loop or to its start.

### Loop with several exit conditions

A search loop usually stops either when the item is found or when the data ends:

```nasm
; file: find.asm
global _start

section .data
values dd 7, 3, 9, 4, 8
COUNT  equ 5

section .text
_start:
    xor  ecx, ecx           ; index
.search:
    cmp  ecx, COUNT
    jae  .not_found         ; ran out of data
    cmp  dword [values + rcx*4], 9
    je   .found             ; found the value
    inc  ecx
    jmp  .search
.not_found:
    mov  ecx, 255           ; report 255 for "not found"
.found:
    mov  edi, ecx           ; index of the match
    mov  eax, 60
    syscall                 ; exit status 2 (9 is at index 2)
```

---

## 13.3 The `loop` Instruction

`loop label` is a one-instruction shortcut:

```
rcx = rcx - 1
if rcx != 0: jump to label
```

```nasm
; file: loop_sum.asm
global _start

section .text
_start:
    xor  eax, eax
    mov  ecx, 10            ; loop count in rcx
.next:
    add  eax, ecx
    loop .next              ; dec rcx; jnz .next  (but doesn't touch flags)
    mov  edi, eax
    mov  eax, 60
    syscall                 ; exit status 55
```

Related instructions:

| Instruction | Action |
|-------------|--------|
| `loop label` | `rcx--`; jump if `rcx != 0` |
| `loope` / `loopz label` | `rcx--`; jump if `rcx != 0` **and** `ZF = 1` |
| `loopne` / `loopnz label` | `rcx--`; jump if `rcx != 0` **and** `ZF = 0` |
| `jrcxz label` | Jump if `rcx == 0` (does **not** decrement) |

Important cautions:

- `loop` is **slower** than `dec rcx` + `jnz` on most modern CPUs, which is why compilers never emit it. It is fine for learning and for tiny programs.
- It does **not** modify the flags.
- If `rcx` is 0 when `loop` first executes, it decrements to `0xFFFFFFFFFFFFFFFF` and runs **2⁶⁴ times**. Guard against an empty loop with `jrcxz`:

```nasm
    jrcxz .skip             ; do nothing when the count is zero
.body:
    ; ...
    loop .body
.skip:
```

---

## 13.4 Nested Loops

A loop inside a loop needs a separate counter for each level. The inner counter is reset on every pass through the outer loop.

```c
for (i = 0; i < 3; i++)
    for (j = 0; j < 4; j++)
        count++;
```

```nasm
; file: nested.asm
global _start

section .text
_start:
    xor  eax, eax           ; count = 0
    xor  ebx, ebx           ; i = 0
.outer:
    cmp  ebx, 3
    jge  .done
    xor  ecx, ecx           ; j = 0   (reset every time)
.inner:
    cmp  ecx, 4
    jge  .inner_done
    inc  eax                ; count++
    inc  ecx                ; j++
    jmp  .inner
.inner_done:
    inc  ebx                ; i++
    jmp  .outer
.done:
    mov  edi, eax
    mov  eax, 60
    syscall                 ; exit status 12 (3 * 4)
```

Notes on nested loops:

- Use **different local labels** (`.outer`, `.inner`). Local labels belong to the nearest preceding global label, so two loops in the same procedure need different names.
- If you use `loop` for both levels, you need to **save the outer count** (`rcx` is the only counter), usually by pushing it on the stack or moving it to another register:

```nasm
    mov  rcx, 3
.outer:
    push rcx                ; save the outer counter
    mov  rcx, 4
.inner:
    ; body
    loop .inner
    pop  rcx                ; restore the outer counter
    loop .outer
```

- Cost multiplies: nested loops of *n* and *m* iterations run the body *n × m* times.
- Accessing a two-dimensional array stored row by row uses the index `row * columns + column`. Visit the inner dimension in the order memory is laid out for the best cache behaviour (Lesson 37).

---

[Previous](./[12]-Control-Flow.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[14]-Arrays-and-Strings.md)
