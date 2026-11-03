# Data structures

> A data structure is a way of organising values in memory so that a particular set of operations is fast; choosing one is choosing which operations to optimise and which to make expensive.

**Level:** beginner  
**Time:** 12 minutes  
**Scope:** the core structures and their operation costs. It does not cover advanced persistent structures or lock-free variants.

## The mental model

A toolbox. A hammer is not better than a screwdriver; it is better for nails. An array is great at “give me item 42” and terrible at “insert at the front” unless you move everything.

Where the analogy breaks: the same abstract structure can have very different performance in different languages, allocators, and cache hierarchies.

## The core structures

| Structure | Access | Search | Insert | Delete | Best for |
|---|---|---|---|---|---|
| Array | `O(1)` by index | `O(n)` | `O(n)` middle, `O(1)` end | `O(n)` middle | Ordered data, cache locality |
| Dynamic array | `O(1)` by index | `O(n)` | `O(1)` amortised end | `O(n)` middle | Stacks, lists, buffers |
| Linked list | `O(n)` | `O(n)` | `O(1)` with node | `O(1)` with node | Frequent splicing, unknown size |
| Hash table | `O(1)` average | `O(1)` average | `O(1)` average | `O(1)` average | Lookup by key |
| Balanced BST | `O(log n)` | `O(log n)` | `O(log n)` | `O(log n)` | Ordered range queries |
| Heap | `O(1)` min/max | `O(n)` | `O(log n)` | `O(log n)` | Priority queues |
| Trie | `O(k)` key length | `O(k)` | `O(k)` | `O(k)` | Prefix search, autocomplete |
| Graph | Varies | Varies | Varies | Varies | Relationships and traversal |

## Arrays and dynamic arrays

Arrays store elements contiguously. Indexing is arithmetic: `base + index × size`. Iteration is cache-friendly.

A dynamic array (Python `list`, JavaScript `Array`, C++ `vector`) over-allocates and grows geometrically. Appends are amortised `O(1)`; inserting in the middle shifts elements.

Use an array when you need index access, iteration, or a compact memory layout.

## Linked lists

Each node stores a value and a pointer to the next (and sometimes previous) node. Insertion and deletion are `O(1)` **if you already have the node**. Finding the node is `O(n)`.

Linked lists are pointer-heavy and cache-unfriendly. On modern CPUs, a dynamic array often beats a linked list even for middle insertions until the list is large. Use them when splicing is the dominant operation or the structure is shared.

## Hash tables

A hash function maps a key to a bucket. Collisions are resolved by chaining or open addressing.

- Average `O(1)` lookup, insert, delete.
- Worst case `O(n)` when everything collides.
- Iteration order is usually unspecified.
- Keys need stable equality and hashing.

Hash tables are the default for “look up by ID.” They do not support range queries or sorted iteration.

## Balanced trees

A balanced binary search tree keeps height `O(log n)`. In-order traversal is sorted. B-trees, red-black trees, and AVL trees are variants tuned for different storage.

Use a tree when you need ordered operations: range scans, predecessor/successor, or sorted iteration. Databases use B+ trees because they are designed for block storage and range scans.

## Heaps

A heap is a complete binary tree with the heap property: every parent is ≤ or ≥ its children. It gives `O(1)` access to the min/max and `O(log n)` insertion/extraction.

Use it for:

- priority queues,
- schedulers,
- top-k problems,
- Dijkstra's algorithm.

A heap is not sorted. Iterating it does not produce sorted order.

## Graphs

A graph is vertices plus edges. Representations:

| Representation | Space | Edge lookup | Iteration |
|---|---|---|---|
| Adjacency list | `O(V + E)` | `O(degree)` | Efficient |
| Adjacency matrix | `O(V²)` | `O(1)` | Dense graphs |
| Edge list | `O(E)` | `O(E)` | Simple input |

Traversal: BFS for shortest path in an unweighted graph, DFS for connectivity and cycles, Dijkstra for non-negative weighted shortest paths, topological sort for dependencies.

## Tries

A trie stores characters along edges. It supports prefix queries and autocomplete in `O(k)`, where `k` is the key length.

The cost is memory: each node may store many child pointers. Compressed tries (radix trees) reduce that cost.

## Choosing a structure

Ask:

1. What is the dominant operation? Lookup, insert, delete, range, or traversal?
2. Do I need ordering?
3. Is the data small enough that a simple array wins?
4. What is the memory budget?
5. Is concurrency involved?

A hash map plus an array often beats a clever tree. A database index is a B-tree because the access pattern is range-heavy and disk-oriented.

## Failure modes

| Failure | Cause | Fix |
|---|---|---|
| Hash collisions | Poor hash or adversarial keys | Randomised hashing, better function |
| Unbounded cache | No eviction | LRU/TTL, size cap |
| O(n²) hidden loop | `in` on a list inside a loop | Convert to a set/map |
| Linked-list traversal | Pointer chasing | Use an array or index |
| Priority inversion | Heap ordering wrong direction | Check comparator |
| Graph memory blowup | Dense adjacency matrix | Adjacency list |

## Check your understanding

1. Why is a hash table lookup `O(1)` average but not guaranteed?
2. When would a linked list beat a dynamic array despite worse cache locality?
3. Why does a database use a B+ tree instead of a binary search tree?

## Primary sources

- Cormen et al., [Introduction to Algorithms](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/).
- Sedgewick and Wayne, [Algorithms](https://algs4.cs.princeton.edu/home/).
- VisuAlgo, [Data structure visualisations](https://visualgo.net/en).
- Python, [Data structures tutorial](https://docs.python.org/3/tutorial/datastructures.html).

> **Next:** [The Tech Atlas home](../README.md) for the full map.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
