[Previous](./[8]-Graph-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[10]-Bit-Manipulation.md)

# Lesson 9 - String Algorithms

## 9.1 Pattern Matching Basics

**Pattern matching** means finding whether (and where) a smaller string, the **pattern**, occurs inside a larger string, the **text**. It's the problem behind text editor "find", DNA sequence matching, spam filters, and much more.

The simplest possible approach is the **naive (brute-force) search**: try matching the pattern starting at every possible position in the text.

```python
def naive_search(text, pattern):
    n, m = len(text), len(pattern)
    positions = []

    for i in range(n - m + 1):
        if text[i:i + m] == pattern:
            positions.append(i)

    return positions

print(naive_search("ababcababcab", "abcab"))  # [2, 7]
```

This works, but it's **O(n · m)** in the worst case — for every one of the n possible starting positions, comparing the pattern character-by-character can take up to m steps (e.g. searching for `"aaaa...b"` inside `"aaaa...a"`). The rest of this lesson covers algorithms designed to avoid that repeated work.

---

## 9.2 Common String Algorithms (Naive Search, KMP, Rabin-Karp Overview)

**Knuth-Morris-Pratt (KMP)** speeds up pattern matching by never re-examining characters in the text it has already looked at. It precomputes a **"failure function"** (also called the **LPS array**, for "longest proper prefix which is also a suffix") for the pattern, which tells it, after a partial match fails, exactly how far it can safely skip ahead instead of restarting from scratch.

```python
def build_lps(pattern):
    lps = [0] * len(pattern)
    length = 0
    i = 1

    while i < len(pattern):
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        elif length != 0:
            length = lps[length - 1]
        else:
            lps[i] = 0
            i += 1

    return lps

def kmp_search(text, pattern):
    n, m = len(text), len(pattern)
    lps = build_lps(pattern)
    positions = []
    i = j = 0  # i indexes text, j indexes pattern

    while i < n:
        if text[i] == pattern[j]:
            i += 1
            j += 1
            if j == m:
                positions.append(i - j)
                j = lps[j - 1]
        elif j != 0:
            j = lps[j - 1]  # skip ahead using the failure function
        else:
            i += 1

    return positions

print(kmp_search("ababcababcab", "abcab"))  # [2, 7]
```

Because it never backs up within the text, KMP guarantees **O(n + m)** time — a significant improvement over naive search's O(n · m), especially for large texts or patterns with a lot of internal repetition.

**Rabin-Karp** takes a different approach: it computes a **hash** of the pattern, then slides a window across the text computing a **rolling hash** for each substring of the same length, only doing a full character-by-character comparison when the hashes match (since different substrings can occasionally hash to the same value — a "hash collision" — so a hash match must still be verified).

```python
def rabin_karp_search(text, pattern, base=256, prime=101):
    n, m = len(text), len(pattern)
    if m > n:
        return []

    pattern_hash = 0
    window_hash = 0
    h = pow(base, m - 1, prime)
    positions = []

    for i in range(m):
        pattern_hash = (base * pattern_hash + ord(pattern[i])) % prime
        window_hash = (base * window_hash + ord(text[i])) % prime

    for i in range(n - m + 1):
        if pattern_hash == window_hash:      # hash matches, verify with a real comparison
            if text[i:i + m] == pattern:
                positions.append(i)

        if i < n - m:
            # roll the hash forward: drop the leftmost char, add the next one
            window_hash = (base * (window_hash - ord(text[i]) * h) + ord(text[i + m])) % prime
            window_hash = (window_hash + prime) % prime  # keep it non-negative

    return positions

print(rabin_karp_search("ababcababcab", "abcab"))  # [2, 7]
```

Rabin-Karp runs in **O(n + m)** on average (thanks to the O(1) rolling hash update), but degrades to **O(n · m)** in the worst case if there are many hash collisions. Its rolling-hash idea is especially useful when searching for **multiple patterns at once**, since each pattern's hash can be checked against the same rolling window cheaply.

| Algorithm    | Average Time | Worst Case | Notes                                     |
|--------------|---------------|------------|--------------------------------------------|
| Naive Search | O(n · m)      | O(n · m)   | Simplest to write, fine for small inputs   |
| KMP          | O(n + m)      | O(n + m)   | Best worst-case guarantee, no re-scanning  |
| Rabin-Karp   | O(n + m)      | O(n · m)   | Great for multi-pattern search via hashing |

[Previous](./[8]-Graph-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[10]-Bit-Manipulation.md)
