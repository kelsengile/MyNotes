[Previous](./%5B12%5D-Error-Handling%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[14]-Objects-and-Object-Methods.md)

*Data Structures*

# Lesson 13 - Arrays And Array Methods

## 13.1 Creating And Accessing Arrays

An **array** is an ordered list of values:

```js
const fruits = ["apple", "banana", "cherry"];

fruits[0];        // "apple"  — arrays are zero-indexed
fruits[2];         // "cherry"
fruits.length;    // 3
fruits[fruits.length - 1]; // "cherry" — last item
```

Arrays can hold any mix of types, including other arrays:

```js
const mixed = [1, "two", true, [4, 5]];
```

---

## 13.2 Adding And Removing Items

```js
const nums = [1, 2, 3];

nums.push(4);       // [1, 2, 3, 4]     — add to end
nums.pop();          // [1, 2, 3]         — remove from end, returns removed item
nums.unshift(0);    // [0, 1, 2, 3]      — add to start
nums.shift();        // [1, 2, 3]         — remove from start, returns removed item

nums.splice(1, 1);          // remove 1 item at index 1 → [1, 3]
nums.splice(1, 0, 5, 6);    // insert 5, 6 at index 1 → [1, 5, 6, 3]
```

`push`/`pop` (end of the array) are generally faster than `unshift`/`shift` (start of the array), since the latter requires re-indexing every other item.

---

## 13.3 Finding And Checking Items

```js
const nums = [10, 20, 30, 40];

nums.includes(30);       // true
nums.indexOf(30);         // 2
nums.find(n => n > 25);  // 30  — first match
nums.findIndex(n => n > 25); // 2
```

---

## 13.4 Transforming Arrays: map, filter, reduce

These three methods are the backbone of everyday JavaScript array work — each returns a new result without modifying the original array.

**`map`** transforms every item into something new:

```js
const nums = [1, 2, 3];
const doubled = nums.map(n => n * 2);
console.log(doubled); // [2, 4, 6]
```

**`filter`** keeps only items that pass a test:

```js
const nums = [1, 2, 3, 4, 5, 6];
const evens = nums.filter(n => n % 2 === 0);
console.log(evens); // [2, 4, 6]
```

**`reduce`** combines every item into a single value:

```js
const nums = [1, 2, 3, 4];
const total = nums.reduce((sum, n) => sum + n, 0);
console.log(total); // 10
```
The `0` is the starting value for `sum`; on each iteration, `reduce` runs the function and carries the result forward.

These are commonly chained together:

```js
const total = [1, 2, 3, 4, 5, 6]
  .filter(n => n % 2 === 0)
  .map(n => n * 10)
  .reduce((sum, n) => sum + n, 0);

console.log(total); // 120
```

---

## 13.5 Iterating: forEach vs. for...of

```js
const fruits = ["apple", "banana"];

fruits.forEach((fruit, index) => {
  console.log(index, fruit);
});
```

`forEach` runs a function once per item but, unlike `map`/`filter`, doesn't return anything useful — use it only for side effects like logging. `for...of` (Lesson 9) is often preferable since it supports `break` and `continue`, which `forEach` does not.

---

## 13.6 Sorting And Reversing

```js
const nums = [3, 1, 4, 1, 5];

nums.sort();                       // [1, 1, 3, 4, 5] — sorts as strings by default!
nums.sort((a, b) => a - b);       // ascending numeric sort
nums.sort((a, b) => b - a);       // descending numeric sort

nums.reverse();                    // reverses the current order in place
```

`sort()` without a comparison function converts items to strings, which produces wrong results for numbers (e.g. `[10, 2, 1].sort()` gives `[1, 10, 2]`). Always pass a comparison function when sorting numbers.

---

## 13.7 Other Useful Methods

```js
const nums = [1, 2, 3, 4, 5];

nums.slice(1, 3);     // [2, 3]           — extracts without modifying original
nums.join("-");        // "1-2-3-4-5"
nums.every(n => n > 0); // true             — do ALL items pass?
nums.some(n => n > 4);  // true             — does AT LEAST ONE pass?
Array.isArray(nums);    // true             — reliable array check

const flat = [1, [2, 3], [4, [5, 6]]];
flat.flat();             // [1, 2, 3, 4, [5, 6]]  — flattens one level
flat.flat(Infinity);     // [1, 2, 3, 4, 5, 6]     — flattens completely
```

[Previous](./%5B12%5D-Error-Handling%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[14]-Objects-and-Object-Methods.md)
