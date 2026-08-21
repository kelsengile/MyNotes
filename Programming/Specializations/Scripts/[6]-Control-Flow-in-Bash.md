[Previous](./[5]-Bash-Syntax-and-Variables.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[7]-Functions-in-Bash.md)

*Shell Scripting Fundamentals*

# Lesson 6 - Control Flow In Bash (Conditionals & Loops)

## 6.1 If Statements

```bash
if [ "$1" == "start" ]; then
    echo "Starting..."
elif [ "$1" == "stop" ]; then
    echo "Stopping..."
else
    echo "Unknown command"
fi
```

Common test operators: `-eq`, `-ne`, `-lt`, `-gt` (numbers); `==`, `!=` (strings); `-f`, `-d` (file/directory exists).

---

## 6.2 For Loops

```bash
for file in *.txt; do
    echo "Processing $file"
done

for i in {1..5}; do
    echo "Iteration $i"
done
```

---

## 6.3 While Loops

```bash
count=0
while [ "$count" -lt 5 ]; do
    echo "Count: $count"
    count=$((count + 1))
done
```

---

## 6.4 Case Statements

`case` is a cleaner alternative to long `if/elif` chains when matching against fixed values:

```bash
case "$1" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    *)
        echo "Usage: $0 {start|stop}"
        ;;
esac
```

---

[Previous](./[5]-Bash-Syntax-and-Variables.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[7]-Functions-in-Bash.md)
