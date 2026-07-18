# Functional Programming Concepts

Functional programming (FP) is a paradigm that treats computation as the evaluation of functions and favors immutable data and expressions over changing state and explicit sequences of commands. Many modern languages aren't purely functional but borrow FP concepts heavily (Python, JavaScript, Java, C#, Kotlin), while others are built around FP as a core philosophy (Haskell, Elixir, Clojure, F#).

---

## 11.1 Immutability

### 11.1.1 What Immutability Means

Data is **immutable** if it cannot be changed after it's created. Instead of modifying data in place, operations produce a **new** copy with the change applied, leaving the original untouched.

```python
# Python — tuples are immutable
point = (1, 2)
# point[0] = 5   # TypeError — tuples cannot be modified

# Lists are mutable
numbers = [1, 2, 3]
numbers.append(4)   # modifies the original list in place

# An "immutable-style" update instead creates a new list
new_numbers = numbers + [5]  # numbers itself is unchanged
```

```javascript
// JavaScript
const point = Object.freeze({ x: 1, y: 2 });
// point.x = 5;   // fails silently (or throws in strict mode)

const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4];  // spread into a new array, original untouched
console.log(numbers);     // [1, 2, 3]
console.log(newNumbers);  // [1, 2, 3, 4]
```

### 11.1.2 Mutable vs. Immutable Update Patterns

```python
# Mutable style (in-place modification)
def add_item_mutable(cart, item):
    cart.append(item)   # mutates the original list
    return cart

# Immutable style (returns a new collection)
def add_item_immutable(cart, item):
    return cart + [item]   # original cart is untouched
```

### 11.1.3 Why Immutability Matters

| Benefit                             | Explanation                                                                 |
|-----------------------------------------|------------------------------------------------------------------------------|
| Predictability                          | Data can't change unexpectedly out from under other parts of the program |
| Easier concurrency                        | No risk of race conditions from multiple threads mutating shared data  |
| Simpler debugging                           | A value's history doesn't matter — it either exists as-is, or it doesn't |
| Safer sharing                                 | Passing data to a function can't result in unexpected external mutation |
| Enables structural sharing/optimization          | Unchanged parts of a data structure can be reused rather than copied |

### 11.1.4 Costs of Immutability

- Creating new copies for every change can use more memory and CPU than mutating in place, especially naively.
- Many functional languages and libraries mitigate this using **persistent data structures** — structures designed to share unchanged parts between the old and new versions rather than copying everything (e.g., Clojure's persistent vectors, Immutable.js).

```python
# Naive immutable update of a large list — O(n) copy every time
big_list = list(range(1000000))
updated = big_list + [1000000]   # copies the whole list
```

### 11.1.5 Immutability by Default in FP Languages

Some languages make immutability the default, requiring explicit opt-in for mutability.

```rust
// Rust — variables are immutable by default
let x = 5;
// x = 6;          // compile error: cannot assign twice to an immutable variable
let mut y = 5;    // must explicitly opt into mutability
y = 6;               // allowed
```

---

## 11.2 First-Class & Pure Functions

### 11.2.1 First-Class Functions

A language has **first-class functions** if functions can be treated like any other value: assigned to variables, stored in data structures, passed as arguments, and returned from other functions. This is foundational to FP — it's what makes composing and passing around behavior possible.

```python
def square(x):
    return x * x

operations = {
    "square": square,          # stored in a data structure
    "double": lambda x: x * 2,
}

print(operations["square"](5))  # 25
```

```javascript
const functions = [
    x => x + 1,
    x => x * 2,
    x => x ** 2,
];
console.log(functions.map(f => f(3)));  // [4, 6, 9]
```

### 11.2.2 Pure Functions (Recap)

A **pure function** always produces the same output for the same input and has no observable side effects (no mutating external state, no I/O). Pure functions are the fundamental building block of FP — programs are built by composing many small pure functions together.

```python
# Pure
def add_tax(price, rate):
    return price * (1 + rate)

# Impure — depends on/modifies external state
tax_rate = 0.08
def add_tax_impure(price):
    return price * (1 + tax_rate)   # depends on external variable, not just its arguments
```

### 11.2.3 Function Composition

FP emphasizes building complex behavior by **composing** small, pure functions — chaining the output of one directly into the input of the next.

```python
def compose(f, g):
    return lambda x: f(g(x))

def double(x): return x * 2
def increment(x): return x + 1

double_then_increment = compose(increment, double)
print(double_then_increment(5))  # double(5)=10, then increment(10)=11
```

```javascript
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);

const double = x => x * 2;
const increment = x => x + 1;

const doubleThenIncrement = pipe(double, increment);
console.log(doubleThenIncrement(5));  // 11
```

### 11.2.4 Currying and Partial Application

**Currying** transforms a function that takes multiple arguments into a sequence of functions that each take a single argument. **Partial application** fixes some arguments of a function in advance, producing a new function with fewer remaining parameters.

```python
# Currying
def add(a):
    def inner(b):
        return a + b
    return inner

add_five = add(5)     # partially applied
print(add_five(3))    # 8
```

```javascript
// JavaScript
const add = a => b => a + b;
const addFive = add(5);
console.log(addFive(3));  // 8

// Partial application with .bind
function multiply(a, b) { return a * b; }
const double = multiply.bind(null, 2);
console.log(double(10));  // 20
```

### 11.2.5 Immutability + Pure Functions Together

Combining these two ideas is central to FP: pure functions that never mutate their inputs, working over immutable data, make the flow of data through a program much easier to trace — a value produced at one step can be trusted not to change unexpectedly by the time it's used later.

---

## 11.3 Map, Filter, Reduce

These three higher-order functions are the workhorses of functional-style data processing, replacing many manual loops with declarative, composable transformations over collections.

### 11.3.1 Map

`map` applies a function to every element of a collection, producing a new collection of the same length with each element transformed.

```python
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x ** 2, numbers))
# Or, more commonly in Python: a list comprehension
squared = [x ** 2 for x in numbers]
print(squared)  # [1, 4, 9, 16, 25]
```

```javascript
const numbers = [1, 2, 3, 4, 5];
const squared = numbers.map(x => x ** 2);
console.log(squared);  // [1, 4, 9, 16, 25]
```

### 11.3.2 Filter

`filter` selects only the elements of a collection that satisfy a given predicate (a function returning `true`/`false`), producing a new collection with just those elements.

```python
numbers = [1, 2, 3, 4, 5, 6]
evens = list(filter(lambda x: x % 2 == 0, numbers))
evens = [x for x in numbers if x % 2 == 0]   # equivalent comprehension
print(evens)  # [2, 4, 6]
```

```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(x => x % 2 === 0);
console.log(evens);  // [2, 4, 6]
```

### 11.3.3 Reduce (Fold)

`reduce` (also called `fold`) combines all elements of a collection into a single accumulated value, by repeatedly applying a combining function.

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]
total = reduce(lambda acc, x: acc + x, numbers, 0)   # 0 is the initial accumulator
print(total)  # 15

# reduce can build up more complex results too, not just numbers
words = ["Hello", "World"]
sentence = reduce(lambda acc, w: acc + " " + w, words)
print(sentence)  # "Hello World"
```

```javascript
const numbers = [1, 2, 3, 4, 5];
const total = numbers.reduce((acc, x) => acc + x, 0);
console.log(total);  // 15

// Reduce can also build objects, e.g. counting occurrences
const words = ["a", "b", "a", "c", "b", "a"];
const counts = words.reduce((acc, word) => {
    acc[word] = (acc[word] || 0) + 1;
    return acc;
}, {});
console.log(counts);  // { a: 3, b: 2, c: 1 }
```

### 11.3.4 Chaining Map, Filter, and Reduce

These functions are commonly chained together to express multi-step data pipelines declaratively.

```javascript
const orders = [
    { item: "book", price: 15, quantity: 2 },
    { item: "pen", price: 2, quantity: 10 },
    { item: "laptop", price: 1000, quantity: 1 },
];

const totalForExpensiveItems = orders
    .filter(order => order.price > 10)                  // keep expensive items
    .map(order => order.price * order.quantity)          // compute line totals
    .reduce((sum, lineTotal) => sum + lineTotal, 0);      // sum them all

console.log(totalForExpensiveItems);  // 1030 (books: 30, laptop: 1000)
```

```python
orders = [
    {"item": "book", "price": 15, "quantity": 2},
    {"item": "pen", "price": 2, "quantity": 10},
    {"item": "laptop", "price": 1000, "quantity": 1},
]

total = sum(
    o["price"] * o["quantity"]
    for o in orders
    if o["price"] > 10
)
print(total)  # 1030
```

### 11.3.5 Declarative vs. Imperative Style

Map/filter/reduce express **what** transformation should happen, rather than **how** to loop through and accomplish it step by step — this is the core distinction between declarative (FP-flavored) and imperative code.

```python
# Imperative — describes the steps to take
evens = []
for x in numbers:
    if x % 2 == 0:
        evens.append(x)

# Declarative — describes the desired result
evens = list(filter(lambda x: x % 2 == 0, numbers))
```

### 11.3.6 Other Common Functional Collection Operations

| Operation              | Purpose                                                  |
|--------------------------|----------------------------------------------------------|
| `find` / `first`             | Get the first element matching a condition           |
| `some` / `any`                 | Check if at least one element matches a condition   |
| `every` / `all`                  | Check if all elements match a condition            |
| `sort`                              | Produce a sorted collection (ideally without mutating the original) |
| `flatMap`                             | Map, then flatten one level of nested collections |
| `zip`                                    | Combine multiple collections element-wise      |

---

## 11.4 When to Use FP vs. OOP

Functional and object-oriented programming aren't mutually exclusive — most modern languages support both, and real-world code often blends them. Choosing an approach (or mixing them) depends on the shape of the problem.

### 11.4.1 Core Philosophical Difference

| Aspect                  | Object-Oriented Programming                     | Functional Programming                              |
|----------------------------|------------------------------------------------------|------------------------------------------------------|
| Core unit                    | Objects (data + behavior bundled together)       | Functions (behavior, applied to data)                |
| State                            | Often mutable, encapsulated within objects     | Favors immutability; state changes produce new values |
| Composition                        | Achieved via objects calling other objects, inheritance, interfaces | Achieved via composing/chaining pure functions |
| Modeling emphasis                     | Modeling entities and their relationships   | Modeling transformations and data flow              |

### 11.4.2 When OOP Tends to Fit Well

- **Modeling real-world entities** with clear identity and evolving state — users, orders, game characters, UI components.
- **Systems with complex, long-lived state** that needs to be managed carefully and encapsulated (e.g., a bank account, a device driver).
- **Large teams/codebases** that benefit from clear interfaces, encapsulation boundaries, and established design patterns for structuring collaboration.
- **GUI frameworks and simulations**, where objects naturally correspond to visual or interactive components with behavior.

```python
class ShoppingCart:
    def __init__(self):
        self.items = []

    def add_item(self, item):
        self.items.append(item)

    def total(self):
        return sum(item.price for item in self.items)
```

### 11.4.3 When FP Tends to Fit Well

- **Data transformation pipelines** — parsing, ETL, analytics, stream processing — where the task is naturally a sequence of transformations over data.
- **Concurrent/parallel systems**, where immutability avoids entire classes of race conditions and makes reasoning about correctness far simpler.
- **Systems requiring high predictability/testability**, such as financial calculations, since pure functions are trivial to test in isolation.
- **Mathematical or algorithmic problems** that map naturally onto function composition and recursion (e.g., compilers, symbolic computation).

```python
def process_orders(orders):
    return (
        sum(o["price"] * o["quantity"] for o in orders if o["price"] > 10)
    )
```

### 11.4.4 Blending Both Paradigms

In practice, most languages and codebases mix paradigms rather than adhering strictly to one:

```python
class OrderProcessor:                      # OOP: encapsulated object with state
    def __init__(self, orders):
        self.orders = orders

    def total_for_expensive_items(self):    # FP: pure, declarative logic inside a method
        return sum(
            o["price"] * o["quantity"]
            for o in self.orders
            if o["price"] > 10
        )
```

A common, practical pattern: use objects to model state and organize a system's overall structure, but implement the actual logic *within* methods using small, pure, composable functions wherever possible — getting the organizational benefits of OOP alongside the predictability benefits of FP.

### 11.4.5 Practical Guidance

- Prefer immutability and pure functions **by default**, even in an OOP-heavy codebase, unless there's a clear reason to mutate state — it tends to reduce bugs.
- Reach for OOP when a problem is centered on entities with identity, encapsulated state, and behavior that naturally belongs together.
- Reach for FP-style code when a problem is centered on transforming data through a pipeline of steps.
- Don't treat this as an either/or decision at the language level — treat it as a set of tools to apply at the level of individual design decisions within a codebase.