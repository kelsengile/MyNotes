[Previous](./[6]-Dynamic-Programming.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[8]-Graph-Algorithms.md)

# Lesson 7 - Backtracking

## 7.1 What Is Backtracking?

Backtracking is a way of exploring all possible solutions to a problem by building a solution incrementally, one choice at a time, and **abandoning ("backtracking" from) a partial solution the moment it can't possibly lead to a valid answer.** It's essentially a smarter version of brute force: instead of generating every complete possibility and checking each one at the end, backtracking checks constraints as early as possible and prunes entire branches of possibilities that are already invalid.

The general shape of a backtracking algorithm:

```python
def backtrack(partial_solution):
    if is_complete(partial_solution):
        record_solution(partial_solution)
        return

    for choice in possible_next_choices(partial_solution):
        if is_valid(partial_solution, choice):
            make_choice(partial_solution, choice)
            backtrack(partial_solution)
            undo_choice(partial_solution, choice)   # the "backtrack" step
```

That "undo" step is the defining feature: after recursing into a choice, backtracking removes it again before trying the next option, so the same `partial_solution` object can be reused across every branch of the search.

A simple example — generating all permutations of a list:

```python
def permutations(nums):
    result = []

    def backtrack(current, remaining):
        if not remaining:
            result.append(current[:])
            return
        for i in range(len(remaining)):
            current.append(remaining[i])
            backtrack(current, remaining[:i] + remaining[i + 1:])
            current.pop()   # undo the choice before trying the next one

    backtrack([], nums)
    return result

print(permutations([1, 2, 3]))
# [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

Backtracking is typically much faster than pure brute force because of **pruning** — checking `is_valid` early means invalid branches are cut off before wasting time exploring everything beneath them. But its worst-case time complexity is still exponential for most problems, since in the worst case it may still need to explore a huge number of branches.

---

## 7.2 Classic Examples (N-Queens, Sudoku)

**N-Queens** — place N chess queens on an N×N board so that no two queens attack each other (no shared row, column, or diagonal). Backtracking places one queen per row, and immediately abandons a placement if it conflicts with any queen already placed.

```python
def solve_n_queens(n):
    solutions = []
    columns = set()
    diagonals = set()      # row - col is constant along a "\" diagonal
    anti_diagonals = set()  # row + col is constant along a "/" diagonal
    board = []

    def backtrack(row):
        if row == n:
            solutions.append(board[:])
            return

        for col in range(n):
            if col in columns or (row - col) in diagonals or (row + col) in anti_diagonals:
                continue  # invalid placement, skip without recursing (pruning)

            columns.add(col)
            diagonals.add(row - col)
            anti_diagonals.add(row + col)
            board.append(col)

            backtrack(row + 1)

            # undo the choice
            columns.remove(col)
            diagonals.remove(row - col)
            anti_diagonals.remove(row + col)
            board.pop()

    backtrack(0)
    return solutions

print(len(solve_n_queens(8)))  # 92 solutions
```

**Sudoku Solver** — fill a 9×9 grid so every row, column, and 3×3 box contains the digits 1–9 exactly once. Backtracking tries each empty cell with digits 1–9, checking validity, and backs off whenever a digit leads to a dead end.

```python
def solve_sudoku(board):
    def is_valid(row, col, num):
        for i in range(9):
            if board[row][i] == num or board[i][col] == num:
                return False
        box_row, box_col = 3 * (row // 3), 3 * (col // 3)
        for i in range(box_row, box_row + 3):
            for j in range(box_col, box_col + 3):
                if board[i][j] == num:
                    return False
        return True

    def backtrack():
        for row in range(9):
            for col in range(9):
                if board[row][col] == 0:
                    for num in range(1, 10):
                        if is_valid(row, col, num):
                            board[row][col] = num
                            if backtrack():
                                return True
                            board[row][col] = 0  # undo the choice
                    return False  # no valid digit worked here
        return True  # no empty cells left

    backtrack()
    return board
```

Other classic backtracking problems include the **subset sum problem**, **combination sum** (finding all combinations of numbers that add to a target), **maze/path-finding puzzles**, and **graph coloring**. The pattern to recognize: the problem asks you to find one or all valid arrangements/combinations that satisfy a set of constraints, and it's possible to check a partial arrangement for validity before it's complete — that's when backtracking's early pruning pays off over a pure brute-force search.

[Previous](./[6]-Dynamic-Programming.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[8]-Graph-Algorithms.md)
