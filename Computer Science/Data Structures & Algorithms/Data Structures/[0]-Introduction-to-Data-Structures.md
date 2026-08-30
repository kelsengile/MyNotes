[⬅ Back to README](../../../README.md)

# Introduction to Data Structures

This note walks through the core data structures every programmer should know — how each one organizes data in memory, what operations it supports, and what those operations cost. It assumes you're comfortable with the ideas from the DSA Fundamentals and Complexity topics.

## Why Data Structures Matter
The data structure you choose determines what your program can do quickly and what it can't. Picking an array when you need fast lookups by key, or a linked list when you need random access, leads to code that's needlessly slow or awkward. Knowing the trade-offs behind each structure is what lets you pick the right tool instead of the familiar one.

---

## Table of Contents  

1. **[Arrays](./[1]-Arrays.md)**  
   1.1 Static vs. Dynamic Arrays  
   1.2 Indexing and Memory Layout  
   1.3 Common Operations and Their Costs  
   1.4 Multi-Dimensional Arrays & Strings as Character Arrays  

2. **[Linked Lists](./[2]-Linked-Lists.md)**  
   2.1 Singly Linked Lists  
   2.2 Doubly Linked Lists  
   2.3 Circular Linked Lists  
   2.4 Arrays vs. Linked Lists  

3. **[Stacks](./[3]-Stacks.md)**  
   3.1 The LIFO Principle  
   3.2 Array-Based vs. Linked Implementations  
   3.3 Common Use Cases  

4. **[Queues](./[4]-Queues.md)**  
   4.1 The FIFO Principle  
   4.2 Circular Queues  
   4.3 Deques and Priority Queues  

5. **[Hash Tables](./[5]-Hash-Tables.md)**  
   5.1 Hashing and Hash Functions  
   5.2 Collision Resolution (Chaining, Open Addressing)  
   5.3 Load Factor and Resizing  

6. **[Sets](./[6]-Sets.md)**  
   6.1 The Set ADT (Uniqueness, No Order Guarantee)  
   6.2 Hash-Based Sets vs. Tree-Based Sets  
   6.3 Common Set Operations (Union, Intersection, Difference)  

7. **[Trees](./[7]-Trees.md)**  
   7.1 Binary Trees  
   7.2 Binary Search Trees  
   7.3 Balanced Trees (AVL, Red-Black — Overview)  
   7.4 Tree Traversals  

8. **[Heaps](./[8]-Heaps.md)**  
   8.1 Min-Heaps and Max-Heaps  
   8.2 Heap Operations (Insert, Extract)  
   8.3 Heaps and Priority Queues  

9. **[Graphs](./[9]-Graphs.md)**  
   9.1 Graph Terminology  
   9.2 Adjacency List vs. Adjacency Matrix  
   9.3 Directed vs. Undirected, Weighted vs. Unweighted  

10. **[Union-Find (Disjoint Set)](./[10]-Union-Find-Disjoint-Set.md)**  
    10.1 The Disjoint Set ADT  
    10.2 Union by Rank/Size and Path Compression  
    10.3 Use Cases (Cycle Detection, Kruskal's Algorithm)  

11. **[Tries](./[11]-Tries.md)**  
    11.1 What Is a Trie?  
    11.2 Use Cases (Autocomplete, Prefix Search)  

12. **[Advanced & Specialized Structures](./[12]-Advanced-And-Specialized-Structures.md)**  
    12.1 Segment Trees  
    12.2 Fenwick Trees (Binary Indexed Trees)  
    12.3 B-Trees (and Why Databases Use Them)  
    12.4 Skip Lists  
    12.5 Bloom Filters  
