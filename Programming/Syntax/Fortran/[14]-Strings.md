[Previous](./[13]-Allocatable-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[15]-Functions-and-Subroutines.md)

*Arrays & Strings*

# Lesson 14 - Strings & Characters

Programs deal with words as well as numbers: names, messages, file names, column headings. In Fortran, text is stored in `character` variables, and a string is in effect an array of characters with a length. This lesson covers how fixed-length strings behave (they are padded with blanks, which surprises many newcomers), how to cut and join strings, the intrinsic functions that search them, and the flexible deferred-length strings of modern Fortran.

---

## 14.1 Fixed-Length Strings and `trim`

When you declare a `character` variable you choose its **length**, the number of characters it always holds:

```fortran
character(len=10) :: word      ! always exactly 10 characters
character :: letter            ! no length given means len=1
character(len=*), parameter :: title = "Fortran Course"   ! length taken from the value
```

### Padding and truncation

A fixed-length string always has the same length, whatever you put in it:

- If the text is **shorter** than the variable, it is **padded with blanks** on the right.
- If the text is **longer**, it is **cut off** (truncated) on the right, without any warning.

```fortran
word = "Fortran"          ! stored as "Fortran   " (3 blanks added)
word = "Supercomputing"   ! stored as "Supercompu" (4 characters lost)
```

So a `character(len=10)` variable that holds `"Fortran"` still has `len(word)` equal to 10. This is the reason `print *, word` seems to leave extra space after the text.

### Removing and moving the blanks

| Function | Result |
|----------|--------|
| `trim(s)` | `s` without its trailing blanks |
| `adjustl(s)` | Moves the text to the left, adding the removed leading blanks at the end (same length) |
| `adjustr(s)` | Moves the text to the right, with blanks added at the front |
| `len(s)` | The declared length, including padding |
| `len_trim(s)` | The length **without** trailing blanks |
| `repeat(s, n)` | The string `s` repeated `n` times |

`trim` does not change the variable. It gives you a shorter *copy* to use in an expression. Leading blanks are not touched by `trim`, only trailing ones.

### Comparing strings

As Lesson 7.2 explained, when two strings of different lengths are compared, the shorter one is padded with blanks. So trailing blanks never affect `==`:

```fortran
"abc" == "abc   "        ! true
```

### Example

```fortran
program fixed_strings
    implicit none
    character(len=10) :: a
    character(len=4) :: b
    character(len=14) :: c
    character(len=*), parameter :: title = "Fortran Course"

    a = "Fortran"
    b = "Fortran"
    c = "   centered?"

    print '(3A)', "[", a, "]"
    print '(3A)', "[", trim(a), "]"
    print '(3A)', "[", b, "]"
    print *, "len(a), len_trim(a):", len(a), len_trim(a)
    print *, "len(title)         :", len(title)
    print '(3A)', "[", adjustl(c), "]"
    print '(3A)', "[", adjustr(c), "]"
    print *, "a == 'Fortran'     :", a == "Fortran"
    print '(A)', repeat("=", len(title))
    print '(A)', title
    print '(A)', repeat("=", len(title))
end program fixed_strings
```

Output:

```
[Fortran   ]
[Fortran]
[Fort]
 len(a), len_trim(a):          10           7
 len(title)         :          14
[centered?     ]
[     centered?]
 a == 'Fortran'     : T
==============
Fortran Course
==============
```

The string `b` has length 4, so it keeps only `"Fort"` (`gfortran -Wall` warns about this truncation when it can see it at compile time). When you need text with no trailing blanks inside a longer message, always wrap the variable in `trim`.

---

## 14.2 Substrings and Concatenation

### Substrings

You can pick out a part of a string by writing a range in parentheses after its name. This looks like an array section (Lesson 11.4), but the characters are numbered from 1 and there is only one dimension:

```fortran
s(1:5)       ! characters 1 to 5
s(8:)        ! from character 8 to the end
s(:5)        ! from the start to character 5
s(3:3)       ! a single character (note: s(3) would be an error)
```

A single character must be written as `s(3:3)`, because `s(3)` looks like a function call or array element. A substring can also be assigned to. The rest of the string is not changed:

```fortran
s(1:5) = "HOWDY"
```

### Concatenation

The operator `//` joins two strings into a longer one (Lesson 7.1). Remember that fixed-length variables carry their padding along with them, so trim them first:

```fortran
full = trim(first) // " " // trim(last)
```

Without `trim`, `first // last` would insert all the blanks of `first` between the two names.

### Character codes

Each character has a numeric code (ASCII on practically all systems). Two functions convert between a character and its code:

| Function | Meaning | Example |
|----------|---------|---------|
| `iachar(c)` | Code of the character `c` | `iachar("A")` is 65 |
| `achar(n)` | Character with code `n` | `achar(97)` is `a` |

In ASCII, the lowercase letters are exactly 32 above the uppercase ones, so changing case is a matter of adding or subtracting 32. The same codes make `c >= "a" .and. c <= "z"` a way of testing for a lowercase letter.

### Arrays of strings

Strings and arrays combine freely. Each element of a character array is a full string, and you can take a substring of an element:

```fortran
character(len=8) :: names(3)
names(2)(1:3)       ! the first three characters of the second name
```

### Converting between text and numbers (internal I/O)

The `write` and `read` statements of Lesson 8 can use a **string instead of a unit number**. Writing to a string turns a number into text, and reading from a string turns text into a number. These are called *internal files*, and they are the standard way to convert in either direction:

```fortran
write (buf, '(I0)') 42            ! buf now holds "42"
read (text, *) x                  ! x gets the number written in text
```

### Example

```fortran
program substring_demo
    implicit none
    integer :: i, n
    character(len=20) :: s, first, last, full, buf
    character(len=8) :: names(2)
    character :: c
    real :: x

    s = "Hello, Fortran"
    print '(A)', s(1:5)
    print '(A)', s(8:)
    print '(A)', s(:5)
    print '(A)', s(8:8)

    s(1:5) = "HOWDY"
    print '(A)', trim(s)

    first = "Ada"
    last = "Lovelace"
    full = trim(first) // " " // trim(last)
    print '(3A)', "[", full, "]"
    print '(3A)', "[", first // last, "]"

    do i = 1, len_trim(s)
        c = s(i:i)
        if (c >= "a" .and. c <= "z") s(i:i) = achar(iachar(c) - 32)
    end do
    print '(A)', trim(s)
    print *, "iachar('A') =", iachar("A"), "  achar(97) =", achar(97)

    names = ["Grace   ", "Margaret"]
    print '(A)', names(2)(1:3)

    n = 42
    write (buf, '(I0)') n
    print '(A)', "Step " // trim(buf) // " of 100"
    write (buf, '(F6.2)') 3.14159
    print '(3A)', "[", trim(buf), "]"
    buf = "  2.5e3"
    read (buf, *) x
    print *, "text '2.5e3' read as number:", x, "  doubled:", 2 * x
end program substring_demo
```

Output:

```
Hello
Fortran      
Hello
F
HOWDY, Fortran
[Ada Lovelace        ]
[Ada                 Lovelace            ]
HOWDY, FORTRAN
 iachar('A') =          65   achar(97) =a
Mar
Step 42 of 100
[  3.14]
 text '2.5e3' read as number:   2500.00000       doubled:   5000.00000    
```

Notice how `first // last` pushes `Lovelace` far to the right, because `first` is 20 characters long and carries 17 blanks.

---

## 14.3 `len`, `index`, `scan`, `verify`

Fortran has built-in functions for looking inside strings. Besides `len` and `len_trim` from 14.1, the three below search a string for something and tell you **where** it was found. A result of **0** always means "not found".

| Function | Result |
|----------|--------|
| `index(s, sub)` | Position of the first occurrence of the substring `sub` in `s` |
| `index(s, sub, back=.true.)` | Position of the **last** occurrence |
| `scan(s, set)` | Position of the first character of `s` that is **any one of** the characters in `set` |
| `scan(s, set, back=.true.)` | The same, but searching from the end |
| `verify(s, set)` | Position of the first character of `s` that is **not** in `set` (0 if every character is in `set`) |

The difference between `index` and `scan` is important: `index` looks for a whole *sequence* (`"ab"` means an `a` followed by a `b`), while `scan` looks for *any one* of a collection of characters (`"ab"` means an `a` or a `b`).

`verify` is the natural tool for validating text. For example, to test that a string holds only digits, check that `verify(s, "0123456789")` is 0.

### Other useful functions

| Function | Meaning |
|----------|---------|
| `trim`, `adjustl`, `adjustr` | See 14.1 |
| `repeat(s, n)` | Repeat a string |
| `len_trim(s)` | Length without trailing blanks |
| `lge`, `lgt`, `lle`, `llt` | Compare strings strictly by ASCII order, independent of the processor |

### Example

```fortran
program search_demo
    implicit none
    character(len=*), parameter :: digits = "0123456789"
    character(len=40) :: path
    character(len=12) :: text
    integer :: slash, dot, i, vowels

    path = "/home/user/data/results.final.csv"
    slash = index(path, "/", back=.true.)
    dot = index(path, ".", back=.true.)

    print '(A)', "file name : " // trim(path(slash + 1:))
    print '(A)', "extension : " // trim(path(dot + 1:))
    print '(A)', "directory : " // path(:slash)
    print *, "index of 'data'  :", index(path, "data")
    print *, "index of 'xyz'   :", index(path, "xyz")

    text = "Hello, World"
    print *, "first vowel at   :", scan(text, "aeiou")
    print *, "last vowel at    :", scan(text, "aeiou", back=.true.)
    print *, "first non-letter :", verify(text, "abcdefghijklmnopqrstuvwxyz" // &
                                                "ABCDEFGHIJKLMNOPQRSTUVWXYZ")

    vowels = 0
    do i = 1, len(text)
        if (index("aeiouAEIOU", text(i:i)) > 0) vowels = vowels + 1
    end do
    print *, "number of vowels :", vowels

    print *, "'12345' all digits?", verify("12345", digits) == 0
    print *, "'12a45' first bad  :", verify("12a45", digits)
end program search_demo
```

Output:

```
file name : results.final.csv
extension : csv
directory : /home/user/data/
 index of 'data'  :          12
 index of 'xyz'   :           0
 first vowel at   :           2
 last vowel at    :           9
 first non-letter :           6
 number of vowels :           3
 '12345' all digits? T
 '12a45' first bad  :           3
```

Here the `&` at the end of one line continues the expression onto the next (Lesson 4.4). The searches work from `slash` and `dot` to split the path with substrings.

---

## 14.4 Deferred-Length `character(len=:)`

Fixed-length strings are awkward when you do not know how long the text will be, or when it keeps changing. Fortran 2003 added **deferred-length** strings, which combine the `allocatable` idea of Lesson 13 with text. Declare the length as a colon and add `allocatable`:

```fortran
character(len=:), allocatable :: s
```

Now the length is set automatically by assignment, in the same way that an allocatable array is resized (Lesson 13.3):

```fortran
s = "Fortran"              ! len(s) is 7: no padding, no truncation
s = s // " is fast"        ! len(s) becomes 15
s = ""                     ! an empty string, len(s) is 0
```

There is no padding at all, so you do not need `trim`. This makes deferred-length strings much more pleasant for building messages, reading lines, and returning text from functions.

You can also set the length yourself with a type specification in `allocate`:

```fortran
allocate(character(len=3) :: t)       ! a string of exactly 3 characters
```

### Which kind of string should I use?

| Situation | Use |
|-----------|-----|
| Short text whose maximum length is known (a name, a one-letter code) | `character(len=n)` |
| A named constant | `character(len=*), parameter` |
| A procedure argument that accepts any length (Lesson 15) | `character(len=*)` |
| Text that is built up or whose length is unknown | `character(len=:), allocatable` |

### Example

```fortran
program deferred_demo
    implicit none
    integer :: i
    character(len=:), allocatable :: s, list
    character(len=12) :: num

    s = "Fortran"
    print *, "len =", len(s), "  [" // s // "]"

    s = s // " is fast"
    print *, "len =", len(s), "  [" // s // "]"

    s = s(1:7)
    print *, "len =", len(s), "  [" // s // "]"

    list = ""
    do i = 1, 5
        write (num, '(I0)') i * i
        list = list // trim(num)
        if (i < 5) list = list // ", "
    end do
    print *, "squares: ", list
    print *, "len(list) =", len(list)
end program deferred_demo
```

Output:

```
 len =           7   [Fortran]
 len =          15   [Fortran is fast]
 len =           7   [Fortran]
 squares: 1, 4, 9, 16, 25
 len(list) =          15
```

Each assignment gave `s` exactly the length it needed. Combine deferred-length strings with the search functions of 14.3 and you can write flexible text-processing code without ever worrying about padding.

---

[Previous](./[13]-Allocatable-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[15]-Functions-and-Subroutines.md)
