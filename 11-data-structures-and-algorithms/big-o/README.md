# Big O Notation

## What Is Big O?

Big O notation describes how the runtime (or memory usage) of an algorithm grows as the input size `n` grows. It characterises the **worst-case upper bound** on growth, ignoring constant factors and lower-order terms.

It answers the question: *if I double the input size, how much longer does this take?*

Big O is not about wall-clock time — the same O(n) algorithm on a fast machine may outrun an O(log n) algorithm on a slow one for small inputs. Big O describes the shape of growth, which dominates at scale.

---

## Time Complexity vs Space Complexity

**Time complexity** — how many operations does the algorithm perform as `n` grows?  
**Space complexity** — how much additional memory does the algorithm use as `n` grows?

An algorithm can be fast but memory-hungry, or slow but memory-efficient. You often trade one for the other.

---

## Common Complexities

### O(1) — Constant Time

The operation takes the same time regardless of input size.

```java
// Accessing an array element by index — always one operation
int first = array[0];

// HashMap get — hash function + array access
String value = map.get("key");
```

### O(log n) — Logarithmic Time

The input is halved (or divided by a constant) with each step. Very efficient for large inputs.

```java
// Binary search on a sorted array
// 1,000,000 elements → at most ~20 comparisons (log₂ 1,000,000 ≈ 20)
int index = Arrays.binarySearch(sortedArray, target);
```

Each level of a balanced binary tree halves the remaining nodes to search.

### O(n) — Linear Time

The number of operations grows proportionally with input size. Doubling `n` doubles the work.

```java
// Finding an element in an unsorted array — may need to check every element
for (int element : array) {
    if (element == target) return true;
}

// Reading every line of a file — one pass
Files.lines(path).forEach(System.out::println);
```

### O(n log n) — Linearithmic Time

Common in efficient sorting algorithms. Grows faster than linear but much slower than O(n²).

```java
// Merge sort, quick sort, heap sort — Java's built-in Arrays.sort()
Arrays.sort(array);
Collections.sort(list);
```

### O(n²) — Quadratic Time

For every element, you do work proportional to the entire input. Usually caused by nested loops over the same data.

```java
// Bubble sort — for each of n elements, compare with up to n other elements
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n - i - 1; j++) {
        if (array[j] > array[j + 1]) swap(array, j, j + 1);
    }
}
```

At n=10,000: ~100,000,000 operations. At n=100,000: ~10,000,000,000 operations. This does not scale.

### O(2ⁿ) — Exponential Time

Doubling the input squares the work. Appears in naive recursive solutions without memoisation (e.g. naive Fibonacci). Unusable for large inputs.

---

## Best, Worst, and Average Case

For the same algorithm, complexity can differ depending on the input:

| Case | Meaning | Example (Linear Search) |
|------|---------|------------------------|
| Best case | Most favourable input | Target is the first element → O(1) |
| Worst case | Least favourable input | Target is last or absent → O(n) |
| Average case | Expected performance over all inputs | Target is in the middle → O(n/2) = O(n) |

Big O typically expresses the **worst case** unless specified otherwise.

---

## Growth Rate Chart

```
Operations
│                                               O(2ⁿ)
│                                        ╱
│                                   ╱
│                             ╱
│                        O(n²)
│                   ╱
│              ╱
│         ╱         O(n log n)
│    ╱         ╱
│         ╱
│    ──────────────── O(n)
│    ─────────── O(log n)
│    ─────── O(1)
└──────────────────────────────────────► n (input size)
```

---

## Java Collections Operation Complexity

| Data Structure | Add | Remove | Get by index | Search (contains) | Notes |
|---------------|-----|--------|--------------|-------------------|-------|
| `ArrayList` | O(1) amortised | O(n) | O(1) | O(n) | Backed by array; shifts on remove |
| `LinkedList` | O(1) | O(1) if at head/tail | O(n) | O(n) | No random access |
| `HashMap` | O(1) avg | O(1) avg | O(1) avg | O(1) avg | O(n) worst case with hash collision |
| `TreeMap` | O(log n) | O(log n) | O(log n) | O(log n) | Sorted by key; backed by red-black tree |
| `HashSet` | O(1) avg | O(1) avg | — | O(1) avg | No duplicates; backed by HashMap |
| `TreeSet` | O(log n) | O(log n) | — | O(log n) | Sorted, no duplicates |
| `PriorityQueue` | O(log n) | O(log n) | — | O(n) | Min-heap; peek is O(1) |
| `ArrayDeque` | O(1) | O(1) | — | O(n) | Best stack/queue implementation |

---

## Practical Advice

**Don't optimise prematurely.** Write the clearest code first. Only optimise when a profiler shows a real bottleneck.

**Understand your data size.** An O(n²) algorithm on 100 elements is fine. On 1,000,000 elements, it will kill your application.

**Choose the right collection.** Using `ArrayList` to check "does this set contain element X?" in a loop is O(n). Using `HashSet` makes it O(1).

```java
// BAD — O(n) per lookup; full loop is O(n²)
List<String> existing = new ArrayList<>(existingEmails);
for (String email : newEmails) {
    if (existing.contains(email)) { ... }
}

// GOOD — O(1) per lookup; full loop is O(n)
Set<String> existing = new HashSet<>(existingEmails);
for (String email : newEmails) {
    if (existing.contains(email)) { ... }
}
```
