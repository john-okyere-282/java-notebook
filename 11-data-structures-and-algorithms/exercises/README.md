# Phase 11 Exercises — Data Structures and Algorithms

---

## Exercise 1 — Big O Analysis

For each of the following code snippets, state the time complexity in Big O notation and explain your reasoning:

```java
// (a)
int sum = 0;
for (int i = 0; i < n; i++) {
    sum += i;
}

// (b)
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        System.out.println(i + ", " + j);
    }
}

// (c)
int i = n;
while (i > 1) {
    System.out.println(i);
    i = i / 2;
}

// (d)
int result = array[n / 2];

// (e)
for (int i = 0; i < n; i++) {
    for (int j = i; j < n; j++) {
        System.out.println(array[j]);
    }
}
```

---

## Exercise 2 — Linear Search

Implement linear search for `String[]`. The method should return the index of the first match (case-insensitive), or -1. Write tests covering: found at start, found at end, not found, empty array, null input.

---

## Exercise 3 — Binary Search

Implement binary search on a sorted `int[]` from scratch (do not use `Arrays.binarySearch()`). Then verify your result matches `Arrays.binarySearch()` for the same inputs. Write tests for: found in middle, found at boundary, not found, single-element array, two-element array.

---

## Exercise 4 — Count Occurrences in Sorted Array

Given a sorted array and a target, write an O(log n) method that returns how many times the target appears. Example: `[1, 2, 2, 2, 3, 4]`, target `2` → `3`. Hint: find the first and last occurrence using modified binary search.

---

## Exercise 5 — Bubble Sort with Pass Count

Implement bubble sort and add a counter for the number of swaps performed. Run it on:
- An already-sorted array
- A reverse-sorted array
- A random array of 10 elements

Compare the swap counts and explain what this tells you about performance.

---

## Exercise 6 — Merge Sort

Implement merge sort. Write a test that:
1. Generates an array of 10,000 random integers.
2. Sorts it with your merge sort.
3. Verifies the result matches `Arrays.sort()`.
4. Measures and prints the time taken for each.

---

## Exercise 7 — Sort Custom Objects

Create an `Employee` record with `name`, `department`, and `salary`. Given a list of 10 employees, sort them:
1. By salary descending.
2. By department ascending, then by name ascending within each department.
3. By name length ascending.

Use only `Comparator`-based sorting (`list.sort()` or `Collections.sort()`) — do not implement a sorting algorithm.

---

## Exercise 8 — Linked List: Build and Traverse

Implement the singly linked list from the notes (without looking at the solution). Add: insert at front, insert at end, delete by value, print, size. Write a main method demonstrating all operations.

---

## Exercise 9 — Linked List: Find Middle Element

Write a method `findMiddle(Node head)` that returns the middle node's value in a single pass (O(n) time, O(1) space). Hint: use two pointers — slow moves one step, fast moves two steps. When fast reaches the end, slow is at the middle.

---

## Exercise 10 — Linked List: Detect Cycle

Write a method `hasCycle(Node head)` that returns `true` if the linked list contains a cycle (a node's `next` points to an earlier node). Use the two-pointer (Floyd's) algorithm — O(n) time, O(1) space.

---

## Exercise 11 — Stack: Bracket Matching

Implement the bracket matching example from the stacks-queues notes. Extend it to report the index of the first mismatched character. Test cases: `"()[]{}"`, `"{[(])}"`, `"((("`, `""`.

---

## Exercise 12 — Stack: Evaluate Reverse Polish Notation

Write a method that evaluates an expression in Reverse Polish Notation (postfix). Input is an array of strings; each element is either an integer or one of `+`, `-`, `*`, `/`.

Examples:
- `["2", "1", "+", "3", "*"]` → `9` (equivalent to `(2 + 1) * 3`)
- `["4", "13", "5", "/", "+"]` → `6` (equivalent to `4 + (13 / 5)`)

---

## Exercise 13 — Queue: BFS on a Grid

Represent a 5×5 grid as a 2D boolean array where `true` = passable and `false` = wall. Write a BFS method that finds the shortest path from the top-left corner to the bottom-right corner and returns its length (-1 if no path exists). Print the path coordinates.

---

## Exercise 14 — BST: Insert and In-Order Traversal

Build a BST from the following input: `[8, 3, 10, 1, 6, 14, 4, 7, 13]`. Print the tree using in-order traversal and verify that the output is sorted.

---

## Exercise 15 — BST: Validate a BST

Write a method `isValidBST(TreeNode root)` that returns `true` if the tree satisfies the BST property. Test it with a valid BST, a tree that looks like a BST but isn't (e.g. a node in the wrong subtree violates an ancestor's constraint), and an empty tree.

---

## Exercise 16 — Tree: Level-Order Traversal with Levels

Modify the level-order traversal from the notes to return a `List<List<Integer>>` where each inner list is one level of the tree. Example output for the demo tree: `[[10], [5, 15], [3, 7, 20]]`.

---

## Exercise 17 — HashMap Frequency Count

Write a method `topKFrequent(int[] nums, int k)` that returns the `k` most frequent elements. Example: `[1,1,1,2,2,3]`, k=2 → `[1, 2]`. Target O(n log k) or better.

---

## Exercise 18 — Two Sum

Given an array of integers and a target sum, return the indices of the two numbers that add up to the target. Use a `HashMap` for an O(n) solution. Example: `[2, 7, 11, 15]`, target `9` → `[0, 1]`.

---

## Exercise 19 — Merge Two Sorted Arrays

Write a method that merges two sorted arrays into a single sorted array in O(n + m) time. Do not use `Arrays.sort()` — implement the merge manually (this is the merge step of merge sort).

---

## Exercise 20 — Minimum Stack

Design a `MinStack` class that supports `push`, `pop`, `peek`, and `getMin` (returns the minimum element in the stack), all in O(1) time. Hint: use a second stack to track minimums.
