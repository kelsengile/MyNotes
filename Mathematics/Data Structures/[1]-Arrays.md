[Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[2]-Linked-Lists.md)

# Lesson 1 - Arrays

An array is the simplest data structure: a fixed-size, contiguous block of memory that holds elements of the same type, each reachable by an index. Almost every other data structure in this topic is either built on top of an array or exists specifically to work around an array's limitations, so understanding arrays well pays off throughout the rest of the topic.

## 1.1 Static vs. Dynamic Arrays

A **static array** has a size that is fixed when it's created. The compiler or runtime reserves that many contiguous memory slots up front, and the size never changes — if you need to store more elements than you allocated for, you must create a brand new, larger array and copy everything over.

```c
// A static array in C: size 5, fixed forever
int scores[5] = {90, 85, 77, 100, 60};
```

A **dynamic array** (called `list` in Python, `ArrayList` in Java, `Vector` in C++, or just "array" in JavaScript) behaves like it can grow and shrink freely. Under the hood it's still backed by a static array. When it runs out of room, it:

1. Allocates a new, larger backing array (commonly double the old capacity).
2. Copies every existing element into the new array.
3. Frees the old array.
4. Adds the new element.

```python
# Python's list is a dynamic array
scores = [90, 85, 77, 100, 60]
scores.append(72)  # may trigger a resize behind the scenes
```

Because resizing is rare relative to how often you append (doubling means you only resize O(log n) times for n insertions), appending to a dynamic array is **amortized O(1)** — most appends are instant, and the occasional expensive resize averages out over time.

## 1.2 Indexing and Memory Layout

Arrays are fast at reading a specific position because of how they sit in memory. Elements are stored **contiguously** — back to back, with no gaps — so the address of any element can be calculated directly instead of being searched for:

```
address(index i) = base_address + (i * size_of_each_element)
```

This is why `array[i]` is an O(1) operation regardless of how large the array is or where `i` points: it's simple arithmetic, not a search. This also explains why arrays are indexed starting at 0 in most languages — index 0 sits exactly at the base address, with no offset needed.

This contiguous layout has a side effect worth knowing: because the whole array lives in one memory block, arrays play very well with CPU caches (accessing one element tends to pull nearby elements into fast cache memory too), which is part of why arrays often outperform other structures in practice even when their theoretical Big O looks the same.

## 1.3 Common Operations and Their Costs

| Operation | Static Array | Dynamic Array |
|---|---|---|
| Access by index (`arr[i]`) | O(1) | O(1) |
| Search (unsorted, value unknown) | O(n) | O(n) |
| Insert/delete at the end | N/A (fixed size) | O(1) amortized |
| Insert/delete at the start or middle | N/A (fixed size) | O(n) |

Insertion or deletion anywhere except the end is expensive because every element after the target index has to shift over by one position to close or open the gap:

```python
scores = [90, 85, 77, 100]
scores.insert(0, 95)   # shifts 90, 85, 77, 100 each one slot right → O(n)
scores.pop(0)           # shifts everything left again → O(n)
scores.append(60)       # no shifting needed → O(1) amortized
```

Searching an unsorted array requires checking elements one at a time (**linear search**, O(n)). If the array is sorted, you can use **binary search** instead, repeatedly halving the search range, which brings search down to O(log n):

```python
def binary_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1  # not found
```

## 1.4 Multi-Dimensional Arrays & Strings as Character Arrays

A **multi-dimensional array** is an array of arrays. A 2D array (a "matrix" or "grid") is the most common example, used for anything with rows and columns — game boards, images, tables of data:

```python
grid = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]
print(grid[1][2])  # row 1, column 2 → 6
```

In memory, a 2D array is typically stored as one long contiguous block, row after row (this is called **row-major order**), even though we conceptually think of it as rows and columns. Accessing `grid[i][j]` is still O(1) — it's just two arithmetic lookups instead of one.

**Strings as character arrays**: in many languages, a string is literally implemented as an array of characters, which is why indexing into a string (`"hello"[1]` → `'e'`) is also O(1), and why string operations like reversing or checking a substring inherit array-like costs. Some languages (like Python and Java) make strings *immutable* — instead of modifying the array in place, any "change" to a string actually builds a brand new one.

```python
name = "hello"
print(name[1])       # 'e' — O(1) index access
reversed_name = name[::-1]  # 'olleh' — builds a new string, O(n)
```

**Key takeaway**: reach for an array when you need fast, predictable access by position and your insert/delete activity happens mostly at the end. If you need frequent insertions or deletions in the middle, a different structure — like the linked list covered next — will usually serve you better.

[Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[2]-Linked-Lists.md)
