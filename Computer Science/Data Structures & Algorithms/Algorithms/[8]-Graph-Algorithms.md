[Previous](./[7]-Backtracking.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[9]-String-Algorithms.md)

# Lesson 8 - Graph Algorithms

## 8.1 Breadth-First Search (BFS)

BFS explores a graph level by level: it visits all of a node's direct neighbors before moving on to their neighbors. It's implemented with a **queue** (first-in, first-out).

```python
from collections import deque

def bfs(graph, start):
    visited = {start}
    queue = deque([start])
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)

    return order

graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C', 'E'],
    'E': ['D'],
}
print(bfs(graph, 'A'))  # ['A', 'B', 'C', 'D', 'E']
```

BFS runs in **O(V + E)** time (every vertex and edge is visited once). Its key property: in an **unweighted** graph, the first time BFS reaches a node is guaranteed to be via the shortest possible path, which makes it the go-to algorithm for shortest-path problems when all edges have equal weight.

---

## 8.2 Depth-First Search (DFS)

DFS explores as far as possible along one path before backtracking, using a **stack** (either explicitly, or implicitly via recursion).

```python
def dfs(graph, start, visited=None, order=None):
    if visited is None:
        visited = set()
        order = []

    visited.add(start)
    order.append(start)

    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited, order)

    return order

print(dfs(graph, 'A'))  # ['A', 'B', 'D', 'C', 'E']
```

DFS also runs in **O(V + E)** time. It's the natural choice for problems like detecting cycles, checking whether a graph is connected, finding connected components, solving mazes, and — as we'll see in 8.5 — topological sorting.

---

## 8.3 Shortest Path (Dijkstra's, Bellman-Ford)

When edges have different **weights** (costs), BFS's "first visit = shortest path" guarantee breaks down, and dedicated shortest-path algorithms are needed.

**Dijkstra's Algorithm** finds the shortest path from a start node to every other node, as long as **all edge weights are non-negative**. It's a greedy algorithm (Lesson 5): it always expands the closest unvisited node next, using a min-priority queue to efficiently find that node.

```python
import heapq

def dijkstra(graph, start):
    # graph: {node: [(neighbor, weight), ...]}
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]

    while pq:
        current_dist, node = heapq.heappop(pq)
        if current_dist > distances[node]:
            continue  # already found a shorter path

        for neighbor, weight in graph[node]:
            distance = current_dist + weight
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))

    return distances
```

With a binary heap as the priority queue, Dijkstra's runs in **O((V + E) log V)**.

**Bellman-Ford** solves the same problem but also correctly handles **negative** edge weights, and can detect negative-weight cycles (which make "shortest path" undefined, since you could loop forever to make the path cheaper). It works by relaxing every edge V−1 times.

```python
def bellman_ford(vertices, edges, start):
    # edges: [(u, v, weight), ...]
    distances = {v: float('inf') for v in vertices}
    distances[start] = 0

    for _ in range(len(vertices) - 1):
        for u, v, weight in edges:
            if distances[u] + weight < distances[v]:
                distances[v] = distances[u] + weight

    # one more pass: if anything still improves, there's a negative cycle
    for u, v, weight in edges:
        if distances[u] + weight < distances[v]:
            raise ValueError("Graph contains a negative-weight cycle")

    return distances
```

Bellman-Ford runs in **O(V · E)** — slower than Dijkstra's, so use Dijkstra's whenever you know all weights are non-negative, and reach for Bellman-Ford only when negative weights are possible.

---

## 8.4 Minimum Spanning Trees (Kruskal's, Prim's)

A **minimum spanning tree (MST)** is a subset of a weighted, undirected graph's edges that connects every vertex, contains no cycles, and has the minimum possible total edge weight. Both classic MST algorithms are greedy (Lesson 5).

**Kruskal's Algorithm** sorts all edges by weight and adds each edge to the MST as long as it doesn't create a cycle, using a **union-find (disjoint set)** structure to check for cycles efficiently.

```python
def kruskal(vertices, edges):
    # edges: [(weight, u, v), ...]
    parent = {v: v for v in vertices}

    def find(v):
        while parent[v] != v:
            parent[v] = parent[parent[v]]  # path compression
            v = parent[v]
        return v

    def union(u, v):
        root_u, root_v = find(u), find(v)
        if root_u == root_v:
            return False  # already connected -> would form a cycle
        parent[root_u] = root_v
        return True

    mst = []
    for weight, u, v in sorted(edges):
        if union(u, v):
            mst.append((u, v, weight))

    return mst
```

**Prim's Algorithm** instead grows a single tree from a starting vertex, always adding the cheapest edge that connects the growing tree to a new vertex — similar in spirit to Dijkstra's, also implemented with a priority queue.

```python
import heapq

def prim(graph, start):
    # graph: {node: [(neighbor, weight), ...]}
    visited = {start}
    edges = [(weight, start, neighbor) for neighbor, weight in graph[start]]
    heapq.heapify(edges)
    mst = []

    while edges:
        weight, u, v = heapq.heappop(edges)
        if v in visited:
            continue
        visited.add(v)
        mst.append((u, v, weight))
        for neighbor, w in graph[v]:
            if neighbor not in visited:
                heapq.heappush(edges, (w, v, neighbor))

    return mst
```

Both run in **O(E log V)** with a binary heap. Kruskal's tends to be preferred for sparse graphs (few edges), while Prim's tends to be preferred for dense graphs (many edges).

---

## 8.5 Topological Sort

A topological sort orders the vertices of a **directed acyclic graph (DAG)** so that for every directed edge `u → v`, `u` comes before `v` in the ordering. It's used anywhere tasks have dependencies — build systems, course prerequisites, task scheduling.

One common approach uses DFS: run DFS from every unvisited node, and prepend each node to the result once **all** of its descendants have been fully explored.

```python
def topological_sort(graph):
    visited = set()
    result = []

    def dfs(node):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)
        result.append(node)  # add only after all descendants are processed

    for node in graph:
        if node not in visited:
            dfs(node)

    return result[::-1]  # reverse to get correct order

dag = {
    'shirt': ['jacket'],
    'jacket': [],
    'socks': ['shoes'],
    'shoes': [],
    'underwear': ['pants'],
    'pants': ['shoes', 'jacket'],
}
print(topological_sort(dag))
```

Topological sort runs in **O(V + E)** time, same as a regular DFS traversal, since it's just DFS with one extra bookkeeping step. Topological sort only exists for **acyclic** graphs — a cycle would mean two tasks depend on each other, which has no valid ordering.

[Previous](./[7]-Backtracking.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[9]-String-Algorithms.md)
