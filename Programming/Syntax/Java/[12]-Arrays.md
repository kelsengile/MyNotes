[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[13]-Exception-Handling.md)

*Core Syntax*

# Lesson 12 - Arrays & Multidimensional Arrays

Arrays let you store multiple values of the same type in a single, fixed-size structure. This lesson covers declaring, using, and iterating them, including multidimensional arrays.

## 12.1 Declaring and Creating Arrays

An array's type is written with square brackets. You can create one with a fixed size (elements default to `0`/`false`/`null`) or from literal values:

```java
int[] numbers = new int[5];      // size 5, all elements default to 0
String[] names = new String[3];  // all elements default to null
```

An array's size is fixed at creation time — you can't grow or shrink it afterward.

---

## 12.2 Array Initialization

You can create and populate an array in one step using an initializer list:

```java
int[] scores = {90, 85, 78, 92};
String[] colors = {"red", "green", "blue"};
```

Individual elements are accessed and set using zero-based indices:

```java
scores[0];      // 90
scores[2] = 100; // updates the third element
```

Accessing an index outside the array's bounds (e.g. `scores[10]` on a 4-element array) throws an `ArrayIndexOutOfBoundsException` at runtime — we'll cover exceptions like this in the next lesson.

---

## 12.3 Iterating Arrays

You can loop over an array with a standard indexed `for` loop, or the enhanced for-each loop introduced in [Lesson 9](./[9]-Loops.md):

```java
for (int i = 0; i < scores.length; i++) {
    System.out.println(scores[i]);
}

for (int score : scores) {
    System.out.println(score);
}
```

Note `length` is a *field*, not a method — no parentheses, unlike `String.length()`.

---

## 12.4 Multidimensional Arrays

Java supports arrays of arrays, most commonly two-dimensional arrays representing grids or tables:

```java
int[][] grid = new int[3][3];
grid[0][0] = 1;
grid[1][2] = 5;

int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

for (int[] row : matrix) {
    for (int value : row) {
        System.out.print(value + " ");
    }
    System.out.println();
}
```

Because each "row" is itself an array, rows don't even need to be the same length — this is called a **jagged array**.

---

## 12.5 Arrays Utility Class

The `java.util.Arrays` class provides handy static helper methods for common array operations:

```java
import java.util.Arrays;

int[] nums = {5, 3, 1, 4, 2};
Arrays.sort(nums);                 // sorts in place: [1, 2, 3, 4, 5]
System.out.println(Arrays.toString(nums)); // readable printout
int[] copy = Arrays.copyOf(nums, 3);       // [1, 2, 3]
boolean equal = Arrays.equals(nums, copy); // compares contents
```

Without `Arrays.toString()`, printing an array directly prints an unhelpful memory reference like `[I@1b6d3586` rather than its contents — a common early mistake.

---

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[13]-Exception-Handling.md)