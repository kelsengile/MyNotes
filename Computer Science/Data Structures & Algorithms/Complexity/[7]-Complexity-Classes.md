[Previous](./[6]-Amortized-Analysis.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md)

# Lesson 7 - Complexity Classes

## 7.1 P vs. NP — A Conceptual Overview

Everything covered so far in this topic has analyzed the running time of a *specific* algorithm. **Complexity classes** step back and group entire *problems* — independent of any one algorithm — by how hard they fundamentally are to solve.

**P** ("Polynomial time") is the set of decision problems (problems with a yes/no answer) that **can be solved** by an algorithm running in polynomial time — that is, time bounded by O(n^k) for some constant `k`, like O(n), O(n²), or O(n³). Most of the problems covered elsewhere in these notes — sorting, searching, shortest paths — belong to P: there's a known algorithm that solves them efficiently, with a running time that scales reasonably as input grows.

**NP** ("Nondeterministic Polynomial time") is the set of decision problems where, if you're **given** a proposed solution, you can **verify** whether it's correct in polynomial time — even if *finding* that solution in the first place might take far longer. This is the crucial distinction: NP is not about how hard a problem is to solve, it's about how hard it is to check an answer once someone hands you one.

A classic example: Sudoku. Solving a hard Sudoku puzzle from scratch can take a lot of searching (see the backtracking approach in Lesson 7 of the Algorithms topic). But if someone hands you a **completed** grid and claims it's a valid solution, checking that every row, column, and box contains 1–9 exactly once takes only a quick pass over the grid — clearly polynomial time. So Sudoku-solving is in NP: verification is fast, even though solving is not obviously fast.

Every problem in P is automatically also in NP — if you can *solve* a problem quickly, you can certainly *verify* a proposed solution quickly too (just solve it yourself and compare). This gives `P ⊆ NP`. The famous open question, one of the most important unsolved problems in computer science, is whether the reverse also holds: **does `P = NP`?** In other words, is every problem whose solution can be quickly *verified* also a problem that can be quickly *solved*? Despite decades of effort, nobody has proven it either way — it remains one of the seven Millennium Prize Problems, with a $1,000,000 prize for a correct proof in either direction.

---

## 7.2 Why Some Problems Are Considered "Hard"

Within NP, there's a special subset of problems called **NP-complete**. These are, informally, the "hardest" problems in NP — every other problem in NP can be transformed ("reduced") into an NP-complete problem using only a polynomial amount of extra work. This has a striking consequence: if *anyone* ever found a polynomial-time algorithm for *any single* NP-complete problem, that algorithm could be adapted to solve *every* problem in NP in polynomial time, proving `P = NP` in one stroke. This is exactly why NP-complete problems are the natural focus of the P vs. NP question — they're the problems where a breakthrough would matter the most.

Well-known NP-complete problems include:

- **The Traveling Salesman Problem (decision version)** — is there a route visiting every city exactly once with total distance under some limit `k`?
- **The Subset Sum Problem** — does some subset of a given set of numbers add up to exactly a target value?
- **Graph Coloring** — can a graph's vertices be colored using only `k` colors such that no two adjacent vertices share a color?
- **Boolean Satisfiability (SAT)** — is there an assignment of true/false to variables that makes a given logical formula true? (SAT holds a special place in this list: it was the *first* problem proven to be NP-complete, and every other NP-complete problem's status was established by reducing it to or from SAT.)

For all of these, no polynomial-time algorithm is known — the best known general solutions take exponential time in the worst case, which becomes computationally intractable well before `n` gets very large (recall the O(2ⁿ) and O(n!) growth rates from Lesson 2.3). This is what people mean when they call a problem "hard" in this context: it's not that nobody has been clever enough yet, but that thousands of researchers have tried for decades across an enormous range of these problems without success — a track record strong enough that most computer scientists believe `P ≠ NP`, even without a formal proof.

Knowing a problem is NP-complete is still practically useful, even without an efficient exact solution: it tells you not to waste time hunting for a fast, exact algorithm, and instead to reach for a different strategy — a **greedy approximation** (Lesson 5 of the Algorithms topic) that gets close to optimal quickly, a **heuristic** that works well in practice without a worst-case guarantee, or restricting the problem to a smaller special case that *is* solvable efficiently. Recognizing NP-completeness is often the difference between correctly concluding "an efficient exact algorithm probably doesn't exist for this" versus spending fruitless effort searching for one that was never there to find.

[Previous](./[6]-Amortized-Analysis.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md)
