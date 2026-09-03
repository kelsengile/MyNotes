[Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[2]-Asymptotic-Notation.md)

# Lesson 1 - Why We Analyze Algorithms

## 1.1 Correctness vs. Efficiency

Before an algorithm's speed matters at all, it has to be **correct** — it has to produce the right answer for every valid input, including edge cases like empty inputs, duplicate values, or the largest inputs it's expected to handle. Correctness is a yes/no property: an algorithm either always produces the right output, or it doesn't.

**Efficiency** is a separate question that only makes sense to ask once correctness is established: *how much time and memory does a correct algorithm need, and how does that grow as the input grows?* Two algorithms can both be 100% correct and still be wildly different in practice.

Consider two correct ways to check whether a list contains any duplicate values:

```python
# Version A: compare every pair — correct, but slow
def has_duplicate_a(nums):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] == nums[j]:
                return True
    return False

# Version B: track values seen so far — correct, and fast
def has_duplicate_b(nums):
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False
```

Both functions return the exact same answer for every possible input — they're equally correct. But Version A compares every pair of elements, while Version B only looks at each element once. On a list of 10 items the difference is invisible. On a list of 10 million items, Version A could take hours while Version B finishes in a second. Complexity analysis is the toolkit for predicting that gap *before* running the code, rather than discovering it the hard way in production.

---

## 1.2 Measuring Performance Independent of Hardware

A natural first instinct is to measure an algorithm's speed with a stopwatch — just time how long it takes to run. This works, but it has a serious problem: the result depends on the machine running it, the programming language, the current system load, and even which specific inputs were used for the test. The same algorithm might appear "fast" on a powerful server and "slow" on an old laptop, even though the algorithm itself hasn't changed at all.

What we actually want to know is something about the algorithm itself — a property that holds true regardless of hardware, language, or a particular day's system load. Complexity analysis achieves this by counting **operations relative to input size**, rather than measuring wall-clock time. Instead of asking "how many seconds did this take?", it asks "how does the number of steps grow as the input grows?"

This is why complexity is expressed as a function of `n`, the input size, rather than a number of seconds. Saying an algorithm is "O(n²)" is a statement that will remain true on any computer, in any language, forever — unlike "it took 3.2 seconds on my laptop," which is true for exactly one run, on one machine, with one input.

This hardware-independent way of counting operations is formalized using **asymptotic notation** (most commonly Big-O), which is the subject of the next lesson. It's also the shared language used throughout every algorithm and data structure covered elsewhere in these notes — every time you see a complexity like O(log n) or O(n log n) attached to an algorithm, it's a claim being made using the tools built up across this topic.

[Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[2]-Asymptotic-Notation.md)
