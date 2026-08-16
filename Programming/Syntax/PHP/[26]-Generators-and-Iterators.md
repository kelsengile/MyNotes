[Previous](./[25]-Closures-and-Anonymous-Functions.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[27]-Type-Declarations-and-Strict-Typing.md)

*Advanced Language Features*

# Lesson 26 - Generators And Iterators

## 26.1 What Are Iterators?

An iterator is anything that can be looped over with `foreach`. Arrays are iterable by default, but PHP also lets objects define their own iteration behavior by implementing the built-in `Iterator` interface. Generators, covered next, are a much simpler way to achieve the same thing.

---

## 26.2 Introducing Generators

A generator is a special kind of function that produces a sequence of values one at a time, instead of building and returning a whole array at once. This matters for large or infinite sequences, since values are computed only as they're needed:

```php
<?php
function countTo(int $max) {
    for ($i = 1; $i <= $max; $i++) {
        yield $i;
    }
}

foreach (countTo(5) as $number) {
    echo $number . " "; // 1 2 3 4 5
}
```

---

## 26.3 The yield Keyword

`yield` pauses the function and hands a value back to the caller, then resumes right where it left off on the next iteration. Unlike `return`, a generator function can `yield` many times:

```php
<?php
function readLargeFile(string $path) {
    $handle = fopen($path, 'r');
    while (($line = fgets($handle)) !== false) {
        yield $line;
    }
    fclose($handle);
}

foreach (readLargeFile('data.txt') as $line) {
    echo $line;
}
```

This processes a file line by line without ever loading the whole file into memory.

---

## 26.4 Generators with Keys and yield from

You can yield key-value pairs, just like an associative array:

```php
<?php
function pairs() {
    yield 'a' => 1;
    yield 'b' => 2;
}

foreach (pairs() as $key => $value) {
    echo "$key: $value "; // a: 1 b: 2
}
```

`yield from` delegates to another iterable or generator, flattening its values into the current one:

```php
<?php
function inner() {
    yield 1;
    yield 2;
}

function outer() {
    yield 0;
    yield from inner();
    yield 3;
}

foreach (outer() as $value) {
    echo $value . " "; // 0 1 2 3
}
```

---

[Previous](./[25]-Closures-and-Anonymous-Functions.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[27]-Type-Declarations-and-Strict-Typing.md)
