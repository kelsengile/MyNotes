[Previous](./[38]-Testing-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md)

*Best Practices*

# Lesson 39 - Performance Considerations In Scripting

## 39.1 Avoid Unnecessary Subprocesses

Spawning an external process (like calling `grep` from inside a loop) is expensive relative to using the shell's or language's built-in capabilities:

```bash
# Slower: launches grep once per line
while read -r line; do
    echo "$line" | grep "pattern"
done < file.txt

# Faster: one process handles the whole file
grep "pattern" file.txt
```

---

## 39.2 Efficient File Reading

```python
# Loads the entire file into memory at once — fine for small files
with open("big.log") as f:
    lines = f.readlines()

# Streams line by line — better for very large files
with open("big.log") as f:
    for line in f:
        process(line)
```

---

## 39.3 Batch Operations Instead of Looping

```python
# Slower: one API call per item
for user_id in user_ids:
    requests.get(f"https://api.example.com/users/{user_id}")

# Faster, if the API supports it: one call for many items
requests.get("https://api.example.com/users", params={"ids": ",".join(user_ids)})
```

The same principle applies to databases (batch inserts vs. row-by-row) and file operations (bulk copy vs. per-file loops).

---

## 39.4 Measuring Before Optimizing

```bash
time ./script.sh
```

```python
import time
start = time.perf_counter()
# ... code ...
print(f"Took {time.perf_counter() - start:.2f}s")
```

Don't optimize based on guesses — measure first, identify the actual bottleneck, and only then optimize that specific part. A script that runs once a day rarely needs micro-optimization; a script running thousands of times per minute does.

---

[Previous](./[38]-Testing-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md)
