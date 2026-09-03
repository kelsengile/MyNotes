[Previous](./[10]-Union-Find-Disjoint-Set.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[12]-Advanced-And-Specialized-Structures.md)

# Lesson 11 - Tries

A hash set (Lesson 6) can tell you instantly whether a whole word exists, but it can't efficiently answer a very common related question: "what words *start with* this prefix?" A trie is a tree built specifically around that question, by organizing data character by character instead of value by value.

## 11.1 What Is a Trie?

A **trie** (pronounced "try," from re**trie**val) is a tree where each node represents a single character, and a path from the root down through several nodes spells out a string. Every node holds a map of `character → child node`, plus a flag marking whether the path from the root *to that node* is a complete, valid word (as opposed to just a prefix on the way to a longer word).

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end_of_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True

    def search(self, word):
        node = self._find_node(word)
        return node is not None and node.is_end_of_word

    def starts_with(self, prefix):
        return self._find_node(prefix) is not None

    def _find_node(self, s):
        node = self.root
        for char in s:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
```

```python
trie = Trie()
for word in ["cat", "car", "card", "care", "dog"]:
    trie.insert(word)

trie.search("car")        # True — "car" is a complete word
trie.search("ca")         # False — "ca" is only a prefix, not inserted as a word
trie.starts_with("ca")    # True — several words start with "ca"
trie.starts_with("do")    # True
trie.starts_with("z")     # False
```

Inserting "cat" and "car" share the nodes for `c` and `a`, then branch at `t` vs. `r` — this shared-prefix structure is what makes a trie memory-efficient for large sets of words with common prefixes, and it's exactly what makes prefix queries fast: **insert**, **search**, and **starts_with** all cost O(L), where L is the length of the word or prefix — independent of how many words the trie holds.

## 11.2 Use Cases (Autocomplete, Prefix Search)

**Autocomplete / typeahead search**: as a user types, walk the trie down to the node matching what they've typed so far (O(L)), then explore every path below that node to collect all words with that prefix — exactly the query a hash set cannot answer efficiently, since it would need to check every stored word individually.

```python
def collect_words(node, prefix, results):
    if node.is_end_of_word:
        results.append(prefix)
    for char, child in node.children.items():
        collect_words(child, prefix + char, results)

def autocomplete(trie, prefix):
    node = trie._find_node(prefix)
    if node is None:
        return []
    results = []
    collect_words(node, prefix, results)
    return results

autocomplete(trie, "car")   # ["car", "card", "care"]
```

**Other common use cases**:

- **Spell checkers**: quickly verify whether a word exists, and suggest corrections by exploring nearby paths in the trie.
- **IP routing tables**: routers use trie-like structures to find the longest matching prefix for an IP address.
- **Dictionary implementations** for word games (checking valid Scrabble words, word-search puzzle solvers).
- **Search engines**: storing and querying large vocabularies where prefix matching matters more than exact matching.

The trade-off is memory: a trie generally uses more space than a plain hash set of the same words, since it stores a full tree of nodes down to the character level rather than one entry per word — a cost worth paying whenever prefix-based queries are part of the requirement, and worth avoiding when all you need is exact-match lookups (where a hash set's O(1) average lookup is both simpler and lighter).

[Previous](./[10]-Union-Find-Disjoint-Set.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[12]-Advanced-And-Specialized-Structures.md)
