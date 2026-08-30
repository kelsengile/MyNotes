[Previous](./[3]-Combinatorics.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[5]-Boolean-Algebra.md)

# Lesson 4 - Graph Theory

Graphs model *relationships* between objects: friendships in a social network, links between web pages, dependencies between tasks, or roads between cities. This lesson recaps the core vocabulary, then covers two applications that build directly on it — coloring and trees.

---

## 4.1 Graph Basics (Recap)

A **graph** G = (V, E) consists of a set of **vertices** (or nodes) V and a set of **edges** E, where each edge connects a pair of vertices.

**Key types and terms:**

| Term | Meaning |
|---|---|
| Undirected graph | edges have no direction — {u, v} connects u and v both ways |
| Directed graph (digraph) | edges have direction — (u, v) means "u points to v," not necessarily the reverse |
| Weighted graph | edges carry a numeric cost/weight (e.g. distance, time) |
| Degree of a vertex | the number of edges touching it (in a directed graph, split into in-degree and out-degree) |
| Path | a sequence of vertices connected by edges, with no repeated vertices |
| Cycle | a path that starts and ends at the same vertex |
| Connected graph | there's a path between every pair of vertices |
| Complete graph (Kₙ) | every pair of vertices is connected by an edge |

**Example:** A graph representing a social network might have V = {Alice, Bob, Carol}, and E = {{Alice, Bob}, {Bob, Carol}} — Alice and Bob are friends, Bob and Carol are friends, but Alice and Carol are not directly connected (though there's a path between them through Bob).

**Handshake Theorem:** The sum of all vertex degrees in an undirected graph equals exactly twice the number of edges (since every edge contributes to the degree count of two vertices). This means the sum of degrees is always even — a quick sanity check on any graph you draw.

**Common representations for algorithms:**

- **Adjacency matrix:** an n × n grid where entry (i, j) = 1 if there's an edge between vertex i and j, else 0. Fast to check "are u and v connected?" (O(1)), but wastes space on sparse graphs.
- **Adjacency list:** each vertex stores a list of its neighbors. More space-efficient for sparse graphs (most real-world graphs), and is the standard choice for algorithms like BFS and DFS.

---

## 4.2 Graph Coloring

**Graph coloring** assigns a color to every vertex such that no two *adjacent* vertices (connected by an edge) share the same color. The **chromatic number**, written χ(G), is the minimum number of colors needed.

**Example:** Consider a graph shaped like a triangle (3 vertices, all connected to each other — this is K₃). Every vertex is adjacent to every other vertex, so all three need different colors. χ(K₃) = 3.

**Example:** A graph shaped like a square (4 vertices in a cycle: A–B–C–D–A) can be colored with just 2 colors: A=red, B=blue, C=red, D=blue. No two adjacent vertices share a color, so χ(cycle of even length) = 2.

**Why it matters — the classic real-world motivation:** map coloring. Countries on a map are vertices; two countries sharing a border are connected by an edge. Coloring the graph so no two bordering countries share a color is exactly graph coloring. The famous **Four Color Theorem** states that *any* planar map (drawable without crossing edges) can be colored using at most 4 colors — a landmark result, first proven with the help of a computer in 1976.

**Practical CS applications:**
- **Register allocation** in compilers: variables that are "live" at the same time can't share the same CPU register — this is modeled as a graph coloring problem where variables are vertices and an edge means "these two are alive simultaneously."
- **Scheduling:** exams that share a student are connected by an edge; coloring the graph with `k` colors gives a valid schedule using `k` time slots with no student having two exams at once.

Finding the *exact* chromatic number is NP-hard in general — there's no known fast algorithm for arbitrary graphs, which is why heuristics (like greedy coloring) are used in practice rather than guaranteed-optimal solutions.

---

## 4.3 Trees as a Special Case of Graphs

A **tree** is a connected, undirected graph with no cycles. Trees are one of the most important structures in computer science — file systems, parse trees, decision trees, and binary search trees are all trees.

**Defining properties (any one implies the others, given the graph is connected):**

- A tree with `n` vertices has exactly `n − 1` edges.
- There is exactly **one** unique path between any two vertices (no cycles means no alternate routes).
- Removing any single edge disconnects the tree into two separate pieces.
- Adding any single edge to a tree creates exactly one cycle.

**Example:** A graph with 5 vertices and 4 edges, connected, with no cycles is a tree. If you instead had 5 vertices and 5 edges (still connected), it would necessarily contain a cycle, and would no longer qualify as a tree.

**Rooted trees:** Many trees in CS are **rooted** — one vertex is designated the root, and every other vertex has a well-defined **parent** (the neighbor closer to the root) and possibly **children** (neighbors farther from the root). Vertices with no children are called **leaves**.

| Tree term | Meaning |
|---|---|
| Root | the designated top vertex |
| Parent / Child | direct connection, one level apart |
| Leaf | a vertex with no children |
| Height | the number of edges on the longest path from the root to a leaf |
| Binary tree | a rooted tree where every vertex has at most 2 children |

**Spanning trees:** Given any connected graph G, a **spanning tree** is a subgraph that includes *every vertex* of G, is itself a tree (connected, no cycles), and uses the minimum possible number of edges to do so (n − 1 edges for n vertices). Algorithms like Kruskal's and Prim's find a **minimum spanning tree** — the spanning tree whose total edge weight is smallest — used in network design problems like laying cable to connect a set of cities as cheaply as possible.

---

[Previous](./[3]-Combinatorics.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[5]-Boolean-Algebra.md)
