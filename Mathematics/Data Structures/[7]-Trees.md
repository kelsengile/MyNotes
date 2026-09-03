[Previous](./[6]-Sets.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[8]-Heaps.md)

# Lesson 7 - Trees

Every structure covered so far is essentially **linear** — arrays, linked lists, stacks, and queues all arrange elements in a single sequence. A tree is the first **hierarchical** structure in this topic: elements branch out from a single starting point, which makes trees a natural fit for anything with a nested, parent-child relationship — file systems, organization charts, HTML/DOM, decision logic, and more.

## 7.1 Binary Trees

A tree is built from **nodes**, each holding a value and references to its **children**. The topmost node is the **root**; a node with no children is a **leaf**; the number of edges from the root to a node is that node's **depth**; the longest path from root to any leaf is the tree's **height**.

A **binary tree** restricts every node to at most two children, conventionally called **left** and **right**.

```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

root = TreeNode(10)
root.left = TreeNode(5)
root.right = TreeNode(15)
```

```
        10
       /  \
      5    15
```

A plain binary tree makes no promises about how values relate to their position — that structure comes from the binary search tree, next.

## 7.2 Binary Search Trees

A **binary search tree (BST)** adds one ordering rule to every node: everything in its **left** subtree is smaller, and everything in its **right** subtree is larger.

```python
def insert(node, value):
    if node is None:
        return TreeNode(value)
    if value < node.value:
        node.left = insert(node.left, value)
    else:
        node.right = insert(node.right, value)
    return node

def search(node, value):
    if node is None or node.value == value:
        return node
    if value < node.value:
        return search(node.left, value)
    return search(node.right, value)
```

This rule is what makes a BST fast: at every node, you eliminate an entire half of the remaining tree, the same way binary search eliminates half an array (Lesson 1). Search, insert, and delete are all O(h), where h is the tree's height. If the tree is **balanced** (roughly the same number of nodes on each side at every level), h ≈ log₂(n), so operations run in O(log n).

The catch: nothing about a plain BST *guarantees* balance. Insert values in already-sorted order and the tree degenerates into what's effectively a linked list, with every operation dropping to O(n):

```python
# Inserting 1, 2, 3, 4, 5 in order into a BST with no balancing:
# 1
#  \
#   2
#    \
#     3
#      \
#       4
#        \
#         5     ← a "tree" that's really just a chain, O(n) operations
```

## 7.3 Balanced Trees (AVL, Red-Black — Overview)

A **self-balancing binary search tree** automatically restructures itself after insertions and deletions to keep its height close to log₂(n), guaranteeing O(log n) operations no matter what order values are inserted in.

**AVL trees** enforce a strict balance rule: for every node, the heights of its left and right subtrees can differ by at most 1. After any insert or delete, the tree checks this rule and, if it's violated, performs **rotations** — local rearrangements of a few nodes — to restore it. AVL trees are very tightly balanced, which makes lookups slightly faster, at the cost of more frequent rotations on insert/delete.

**Red-black trees** use a looser rule: each node is colored red or black, and a set of coloring rules (no two red nodes in a row, every root-to-leaf path has the same number of black nodes) guarantees the tree's height never exceeds roughly 2 × log₂(n). This is less tightly balanced than an AVL tree, but requires fewer rotations to maintain, making insert/delete somewhat cheaper on average. Red-black trees are the structure behind many standard library ordered containers, including Java's `TreeMap` and C++'s `std::map`.

Both guarantee O(log n) search, insert, and delete — the difference is a trade-off between lookup speed (AVL, favors read-heavy workloads) and update speed (red-black, favors write-heavy workloads).

## 7.4 Tree Traversals

**Traversal** means visiting every node in a tree exactly once, in some defined order. There are four standard traversals:

**Depth-first traversals** (go as deep as possible before backtracking):

```python
def inorder(node):    # Left, Root, Right — visits BST values in sorted order
    if node:
        inorder(node.left)
        print(node.value)
        inorder(node.right)

def preorder(node):   # Root, Left, Right — useful for copying a tree
    if node:
        print(node.value)
        preorder(node.left)
        preorder(node.right)

def postorder(node):  # Left, Right, Root — useful for safely deleting a tree
    if node:
        postorder(node.left)
        postorder(node.right)
        print(node.value)
```

**Breadth-first traversal** (visit level by level, using a queue from Lesson 4):

```python
from collections import deque

def level_order(root):
    if not root:
        return
    queue = deque([root])
    while queue:
        node = queue.popleft()
        print(node.value)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
```

**In-order traversal is the one worth remembering specifically for BSTs**: because of the left-smaller, right-larger rule, an in-order traversal always visits nodes in ascending sorted order — a useful property whenever you need a BST's contents sorted without a separate sort step.

[Previous](./[6]-Sets.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[8]-Heaps.md)
