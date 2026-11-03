# Big-O notation

> Big-O describes how an algorithm's resource use grows as input size grows, ignoring constants and machine details. It is a model of growth, not a stopwatch.

**Level:** beginner  
**Time:** 10 minutes  
**Scope:** time and space complexity, common classes, and the limits of asymptotic analysis. It does not cover formal proofs or amortised analysis in depth.

## The mental model

Two delivery routes: one is 10 minutes longer but stays roughly the same as the city grows; the other is fast today but doubles with every new district. Big-O asks which route scales.

It ignores whether the first route uses a bicycle or a van. Constants matter in practice, but they do not change the growth curve.

Where the analogy breaks: real inputs are finite and often small. An `O(n²)` algorithm on 20 items can beat an `O(n log n)` algorithm with a large constant.

## The notation family

| Notation | Meaning | Example |
|---|---|---|
| `O(f(n))` | Upper bound on growth | At most linear |
| `Ω(f(n))` | Lower bound | At least linear |
| `Θ(f(n))` | Tight bound | Exactly linear growth |
| `o(f(n))` | Strictly smaller growth | Sublinear relative to f |
| `ω(f(n))` | Strictly larger growth | Superlinear relative to f |

In everyday engineering, “Big-O” usually means the tight upper bound people care about.

## Common growth classes

| Class | Name | n = 10 | n = 1,000 | Typical example |
|---|---|---:|---:|---|
| `O(1)` | Constant | 1 | 1 | Hash lookup, array index |
| `O(log n)` | Logarithmic | ~3 | ~10 | Binary search, balanced tree |
| `O(n)` | Linear | 10 | 1,000 | Single pass |
| `O(n log n)` | Linearithmic | ~33 | ~10,000 | Comparison sort |
| `O(n²)` | Quadratic | 100 | 1,000,000 | Nested loops |
| `O(2ⁿ)` | Exponential | 1,024 | Astronomical | Brute-force subsets |
| `O(n!)` | Factorial | 3.6M | Astronomical | Brute-force permutations |

The table shows operations, not seconds. Constant factors and hardware determine wall-clock time.

## How to read code

```python
# O(n)
for item in items:
    print(item)

# O(n²)
for a in items:
    for b in items:
        compare(a, b)

# O(log n)
while low <= high:
    mid = (low + high) // 2
    ...
```

Nested loops are not automatically `O(n²)` if the inner loop runs a constant number of times. Two sequential loops are `O(n) + O(m)`, which is `O(n + m)`, not `O(n²)`.

## Space complexity

Space complexity counts extra memory as a function of input size.

- In-place sort: `O(1)` or `O(log n)` auxiliary space.
- Merge sort: `O(n)` auxiliary space.
- Recursion: stack depth counts, even if each frame is small.

A streaming algorithm can be `O(1)` space and still be slow. Time and space are separate budgets.

## Amortised cost

A dynamic array doubles its capacity occasionally. A single append may copy `n` elements, but the cost spread over many appends is `O(1)` amortised.

Amortised does not mean “always fast.” It means the average over a sequence of operations is bounded. A latency-sensitive system can still care about the occasional resize.

## Best, average, worst

- **Best case:** the input that finishes fastest.
- **Average case:** expected over a distribution of inputs.
- **Worst case:** the input that takes longest.

Hash tables are `O(1)` average, `O(n)` worst case if every key collides. Quicksort is `O(n log n)` average, `O(n²)` worst case without a good pivot strategy.

## What Big-O hides

- Constants and cache behaviour.
- Memory allocation and garbage collection.
- Network latency and disk I/O.
- Input distribution.
- Parallelism and contention.

A “faster” algorithm can lose on a small input, a hot cache, or a GPU. Complexity is a filter, not the final answer.

## Check your understanding

1. Why is binary search `O(log n)` rather than `O(1)`?
2. Two algorithms are both `O(n log n)`. What else would you measure before choosing?
3. What does it mean that a hash table lookup is `O(1)` average but `O(n)` worst case?

## Primary sources

- Cormen, Leiserson, Rivest, and Stein, [Introduction to Algorithms](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/).
- MIT OpenCourseWare, [6.006 Introduction to Algorithms](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/).
- Sedgewick and Wayne, [Algorithms](https://algs4.cs.princeton.edu/home/).
- Python, [Time complexity wiki](https://wiki.python.org/moin/TimeComplexity).

> **Next:** [Data structures](./data-structures.md) applies complexity to the containers you use every day.

---

**Last reviewed: 2026-11-03.** Corrections welcome; open an issue with a primary source.
