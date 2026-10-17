[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[5]-How-a-CPU-Works.md)

*Foundations*

# Lesson 4 - Number Systems & Data Representation (binary, hex, two's complement)

A CPU stores everything (numbers, text, addresses, instructions) as **bits**. To read registers in a debugger, understand a memory dump, or reason about overflow, you must be comfortable with binary, hexadecimal, and two's complement. This lesson builds those skills from scratch.

---

## 4.1 Binary and Hexadecimal

### Binary (base 2)

A **bit** is a single binary digit: `0` or `1`. In a binary number each position is worth a power of two, counting from the right starting at 2⁰:

```
  1    0    1    1    0    1    0    1      (bits)
 2⁷   2⁶   2⁵   2⁴   2³   2²   2¹   2⁰      (place values)
128   64   32   16    8    4    2    1

128 + 32 + 16 + 4 + 1 = 181
```

So `10110101` in binary is **181** in decimal.

**Decimal to binary:** repeatedly divide by 2 and read the remainders from bottom to top.

```
181 ÷ 2 = 90 remainder 1   ← least significant bit
 90 ÷ 2 = 45 remainder 0
 45 ÷ 2 = 22 remainder 1
 22 ÷ 2 = 11 remainder 0
 11 ÷ 2 =  5 remainder 1
  5 ÷ 2 =  2 remainder 1
  2 ÷ 2 =  1 remainder 0
  1 ÷ 2 =  0 remainder 1   ← most significant bit

Read upward: 10110101
```

Useful powers of two to memorise: 2⁸ = 256, 2¹⁰ = 1,024, 2¹⁶ = 65,536, 2³² = 4,294,967,296.

### Hexadecimal (base 16)

Long binary strings are hard to read. **Hexadecimal** uses 16 symbols, `0-9` and `A-F`, and each hex digit stands for exactly **4 bits** (a *nibble*):

| Hex | Binary | Dec | | Hex | Binary | Dec |
|-----|--------|-----|-|-----|--------|-----|
| 0 | 0000 | 0 | | 8 | 1000 | 8 |
| 1 | 0001 | 1 | | 9 | 1001 | 9 |
| 2 | 0010 | 2 | | A | 1010 | 10 |
| 3 | 0011 | 3 | | B | 1011 | 11 |
| 4 | 0100 | 4 | | C | 1100 | 12 |
| 5 | 0101 | 5 | | D | 1101 | 13 |
| 6 | 0110 | 6 | | E | 1110 | 14 |
| 7 | 0111 | 7 | | F | 1111 | 15 |

To convert binary to hex, group bits in fours from the right:

```
1011 0101  →  B5      (so 10110101 = 0xB5 = 181)
```

Hex is compact: one byte is always exactly two hex digits (`00` to `FF`).

### Writing numbers in NASM

```nasm
mov eax, 181            ; decimal
mov eax, 0xB5           ; hexadecimal (0x prefix)
mov eax, 0B5h           ; hexadecimal (h suffix, must start with a digit)
mov eax, 0b10110101     ; binary (0b prefix)
mov eax, 10110101b      ; binary (b suffix)
mov eax, 0o265          ; octal (0o prefix)
mov eax, 1_000_000      ; underscores are allowed for readability
mov al, 'A'             ; a character: its ASCII code (65)
```

---

## 4.2 Bits, Bytes, Words, and Endianness

### Sizes

Memory is organised in **bytes** (8 bits). x86 gives names to the common sizes:

| Name | Size | NASM data directive | NASM size keyword | Unsigned range |
|------|------|---------------------|-------------------|----------------|
| **Byte** | 8 bits | `db` | `byte` | 0 to 255 |
| **Word** | 16 bits | `dw` | `word` | 0 to 65,535 |
| **Doubleword** (dword) | 32 bits | `dd` | `dword` | 0 to 4,294,967,295 |
| **Quadword** (qword) | 64 bits | `dq` | `qword` | 0 to 18,446,744,073,709,551,615 |

> **Naming trap:** on x86, a "word" is **16 bits** for historical reasons (the original 8086 was a 16-bit CPU), even though today's CPUs are 64-bit. On ARM and RISC-V, "word" usually means 32 bits.

An *n*-bit number holds 2ⁿ distinct values.

### Endianness

A value larger than one byte must be stored as several consecutive bytes, so you must decide **which byte goes first**.

- **Big-endian:** the most significant byte is stored at the lowest address (like writing a number left to right). Used by network protocols.
- **Little-endian:** the **least** significant byte is stored at the lowest address. **x86-64 is little-endian**, as are ARM64 and RISC-V in practice.

For the 32-bit value `0x12345678` stored at address `0x1000`:

```
Address :   0x1000  0x1001  0x1002  0x1003
Little  :     78      56      34      12      ← x86-64
Big     :     12      34      56      78
```

You can see this in a program. The first byte of a `dd 0x12345678` is `0x78` (120 in decimal):

```nasm
; file: endian.asm
global _start

section .data
value dd 0x12345678         ; stored as bytes 78 56 34 12

section .text
_start:
    movzx edi, byte [value] ; read only the first byte (zero-extended)
    mov eax, 60
    syscall                 ; exit status = 0x78 = 120
```

Endianness matters whenever you look at a memory dump, write a binary file, or send data over a network. Bytes inside a register have no "order"; the order is only visible when a value goes to or from memory.

---

## 4.3 Signed Numbers and Two's Complement

The CPU does not know whether a bit pattern is signed or unsigned. The **same bits** can mean different numbers depending on how *you* interpret them. Signed integers on virtually all modern CPUs use **two's complement**.

### The rule

In an *n*-bit two's complement number, the most significant bit (MSB) is the **sign bit**: `1` means negative. Its place value is **negative**: −2ⁿ⁻¹.

For 8 bits:

```
  1     0     0     0     0     0     1     1
-128    64    32    16     8     4     2     1

-128 + 2 + 1 = -125
```

### Negating a number

To compute −x: **invert all the bits, then add 1**.

```
  5   =  0000 0101
invert=  1111 1010
 +1   =  1111 1011   = -5
```

The `neg` instruction does exactly that.

### Ranges

| Size | Signed range |
|------|--------------|
| 8 bits | −128 to 127 |
| 16 bits | −32,768 to 32,767 |
| 32 bits | −2,147,483,648 to 2,147,483,647 |
| 64 bits | −9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

Note the range has one more negative value than positive.

### Same bits, different meanings

| Bits (8-bit) | Hex | As unsigned | As signed |
|--------------|-----|-------------|-----------|
| `0000 0001` | 0x01 | 1 | 1 |
| `0111 1111` | 0x7F | 127 | 127 |
| `1000 0000` | 0x80 | 128 | −128 |
| `1111 1111` | 0xFF | 255 | −1 |

Why two's complement? Addition and subtraction use the **same circuit** for signed and unsigned numbers. Only comparisons and a few instructions (multiplication, division, right shifts, conditional jumps) need to know which interpretation you want.

### Extending a number to a larger size

When you widen a value you must fill the new upper bits correctly:

- **Zero extension** (unsigned): fill with `0`. Instruction: `movzx`.
- **Sign extension** (signed): copy the sign bit. Instructions: `movsx` and `movsxd` (32 to 64 bits).

```nasm
mov al, 0xFF            ; al = 255 unsigned, or -1 signed

movzx ebx, al           ; ebx = 0x000000FF = 255
movsx ecx, al           ; ecx = 0xFFFFFFFF = -1
movsxd rdx, ecx         ; rdx = 0xFFFFFFFFFFFFFFFF = -1
```

---

## 4.4 ASCII and Character Encoding

Computers store text as numbers. **ASCII** assigns a number from 0 to 127 to each basic character, and it fits in one byte.

| Range (dec) | Range (hex) | Contents |
|-------------|-------------|----------|
| 0-31, 127 | 0x00-0x1F, 0x7F | Control characters |
| 32 | 0x20 | Space |
| 48-57 | 0x30-0x39 | Digits `'0'` to `'9'` |
| 65-90 | 0x41-0x5A | Uppercase `'A'` to `'Z'` |
| 97-122 | 0x61-0x7A | Lowercase `'a'` to `'z'` |

Important control characters:

| Name | Dec | Hex | Meaning |
|------|-----|-----|---------|
| NUL | 0 | 0x00 | String terminator in C |
| LF (`\n`) | 10 | 0x0A | Line feed: newline on Linux and macOS |
| CR (`\r`) | 13 | 0x0D | Carriage return (Windows uses CR+LF) |
| TAB | 9 | 0x09 | Horizontal tab |
| ESC | 27 | 0x1B | Escape (starts terminal colour codes) |

Handy patterns you will use constantly:

- **Digit to value:** `'7' - '0' = 7` (subtract `0x30`).
- **Value to digit:** `7 + '0' = '7'`.
- **Upper/lower case differ by one bit:** `'A'` is `0x41` and `'a'` is `0x61`. Bit 5 (`0x20`) is the difference.

```nasm
; file: ascii.asm
global _start

section .text
_start:
    mov al, '5'             ; al = 0x35
    sub al, '0'             ; al = 5, the numeric value of the digit
    movzx edi, al
    mov eax, 60
    syscall                 ; exit status 5
```

### Beyond ASCII

ASCII only covers English letters. **Unicode** assigns a number (a *code point*) to every character in every writing system, and **UTF-8** is the most common way to store it. In UTF-8:

- Code points 0-127 use **one byte**, identical to ASCII.
- Other characters use **2 to 4 bytes**, where every byte has its top bit set.

NASM stores string literals as raw bytes exactly as they appear in your source file, so a UTF-8 source file gives UTF-8 data:

```nasm
msg db "café", 10       ; 'é' takes two bytes (0xC3 0xA9), so this is 6 bytes
```

Because UTF-8 characters vary in size, "number of bytes" and "number of characters" are not the same, and byte-wise tools such as `strlen` count bytes.

---

[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[5]-How-a-CPU-Works.md)
