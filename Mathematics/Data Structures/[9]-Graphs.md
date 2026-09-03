[Previous](./[8]-Heaps.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[10]-Union-Find-Disjoint-Set.md)

# Lesson 9 - Graphs

A tree (Lesson 7) is actually a special case of a more general structure: a graph. Where a tree requires exactly one path between any two nodes and no cycles, a graph drops both restrictions — any node can connect to any number of other nodes, in any pattern, including cycles. This makes graphs the right model for anything with many-to-many relationships: social networks, road maps, computer networks, dependency chains, and more.

## 9.1 Graph Terminology

A graph consists of:

- **Vertices** (or **nodes**): the individual points in the graph.
- **Edges**: the connections between pairs of vertices.

```
    A --- B
    |     |
    C --- D
```

Here, A, B, C, D are vertices, and there are four edges: A-B, A-C, B-D, C-D.

Additional vocabulary worth knowing:

- **Adjacent / neighbors**: two vertices connected directly by an edge.
- **Degree**: the number of edges touching a vertex (A has degree 2 above).
- **Path**: a sequence of edges connecting one vertex to another.
- **Cycle**: a path that starts and ends at the same vertex without repeating any edge.
- **Connected graph**: a graph where a path exists between every pair of vertices.

## 9.2 Adjacency List vs. Adjacency Matrix

There are two standard ways to actually store a graph in memory.

**Adjacency list**: for each vertex, keep a list of its neighbors. Usually implemented as a hash map (Lesson 5) from vertex to a list (or set) of neighboring vertices.

```python
graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A", "D"],
    "D": ["B", "C"],
}
```

**Adjacency matrix**: a 2D array where `matrix[i][j]` is truthy (or holds a weight) if an edge exists between vertex i and vertex j.

```python
#      A  B  C  D
# A  [ 0, 1, 1, 0 ]
# B  [ 1, 0, 0, 1 ]
# C  [ 1, 0, 0, 1 ]
# D  [ 0, 1, 1, 0 ]
```

| | Adjacency List | Adjacency Matrix |
|---|---|---|
| Space | O(V + E) | O(V²) |
| Check if edge (u, v) exists | O(degree of u) | O(1) |
| Find all neighbors of a vertex | O(degree of u) | O(V) |
| Best for | Sparse graphs (few edges) | Dense graphs (many edges) |

(V = number of vertices, E = number of edges.) Most real-world graphs — social networks, road maps, web links — are **sparse** (each vertex connects to relatively few others out of all possible vertices), which is why the adjacency list is the more common choice in practice; the adjacency matrix's O(V²) space becomes wasteful once V grows large.

## 9.3 Directed vs. Undirected, Weighted vs. Unweighted

**Undirected graphs** have edges with no direction — if A connects to B, B also connects to A (like a two-way friendship). **Directed graphs** (digraphs) have edges that point one way — A → B doesn't imply B → A (like a one-way follow relationship, or a one-way street).

```python
# Undirected: adding an edge means updating both sides
graph["A"].append("B")
graph["B"].append("A")

# Directed: only the source vertex records the connection
graph["A"].append("B")   # A → B, but not B → A
```

**Unweighted graphs** treat every edge as equal — only *whether* a connection exists matters. **Weighted graphs** attach a number (a cost, distance, or capacity) to each edge.

```python
# Weighted adjacency list: neighbor paired with edge weight
weighted_graph = {
    "A": [("B", 4), ("C", 1)],
    "B": [("A", 4), ("D", 2)],
    "C": [("A", 1), ("D", 6)],
    "D": [("B", 2), ("C", 6)],
}
```

These two properties combine independently — a graph can be directed and weighted (a flight network, where routes are one-way and have a cost), undirected and unweighted (a simple friendship graph), and so on.

**Traversal**, the graph equivalent of the tree traversals from Lesson 7, comes in the same two flavors:

```python
# Breadth-first search (BFS) — uses a queue, explores level by level
from collections import deque

def bfs(graph, start):
    visited = {start}
    queue = deque([start])
    order = []
    while queue:
        vertex = queue.popleft()
        order.append(vertex)
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return order

# Depth-first search (DFS) — uses a stack (or recursion), explores fully before backtracking
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)
    order = [start]
    for neighbor in graph[start]:
        if neighbor not in visited:
            order += dfs(graph, neighbor, visited)
    return order
```

**Common use cases**: modeling networks and relationships of any kind (social graphs, road networks, the web itself), dependency resolution (build systems, package managers), shortest-path routing (GPS navigation, using algorithms like Dijkstra's, which relies on a heap from Lesson 8), and recommendation systems.

[Previous](./[8]-Heaps.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[10]-Union-Find-Disjoint-Set.md)
