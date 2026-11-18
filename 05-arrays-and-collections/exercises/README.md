# Phase 05 — Arrays and Collections Exercises

Practice exercises covering arrays, ArrayList, Sets, Maps, Queues, Stacks, and Generics.

---

## Exercise 1 — Find Maximum Without Sorting
**Difficulty:** Beginner
**Concepts:** arrays, loop, comparison

### Problem
Write a method `findMax(int[] array)` that returns the maximum value in the array **without** using `Arrays.sort()` or any built-in max utility.

### Expected Output / Sample
```
findMax([3, 7, 1, 9, 4]) → 9
findMax([-5, -1, -8])    → -1
findMax([42])             → 42
```

### Hints
- Start with `max = array[0]`.
- Loop from index 1, update `max` if the current element is larger.

### Bonus
Write a companion `findMin(int[] array)` and `findMinMax(int[] array)` that returns both as an array of two elements.

---

## Exercise 2 — Count Occurrences in an Array
**Difficulty:** Beginner
**Concepts:** arrays, HashMap, iteration

### Problem
Given an array of strings, count how many times each string appears. Print each string and its count.

### Expected Output / Sample
```
Input: ["apple", "banana", "apple", "cherry", "banana", "apple"]
apple  → 3
banana → 2
cherry → 1
```

### Hints
- Use `Map<String, Integer>` with `getOrDefault()`.
- Print using `entrySet()`.

### Bonus
Print the results sorted alphabetically by name.

---

## Exercise 3 — Remove Duplicates Maintaining Order
**Difficulty:** Beginner
**Concepts:** LinkedHashSet, List

### Problem
Write a method `removeDuplicates(List<String> list)` that returns a new list with duplicates removed, maintaining the original order.

### Expected Output / Sample
```
Input:  [Alice, Bob, Alice, Charlie, Bob, Diana]
Output: [Alice, Bob, Charlie, Diana]
```

### Hints
- Use a `LinkedHashSet` to collect unique elements (preserves insertion order).
- Return `new ArrayList<>(set)`.

### Bonus
Write a version that works generically: `removeDuplicates(List<T> list)`.

---

## Exercise 4 — Word Frequency Counter
**Difficulty:** Intermediate
**Concepts:** HashMap, String.split(), sort

### Problem
Write a program that takes a paragraph of text, splits it into words (ignore punctuation and case), and counts the frequency of each word. Print the top 5 most frequent words.

### Expected Output / Sample
```
Input: "To be or not to be that is the question to be answered"
to → 3
be → 3
or → 1
not → 1
that → 1
(top 5)
```

### Hints
- `text.toLowerCase().replaceAll("[^a-z ]", "").split("\\s+")` to clean and split.
- Sort entries by value descending to find top 5.

### Bonus
Print a word frequency histogram using asterisks: `java  ****` (4 occurrences).

---

## Exercise 5 — Implement a Stack Using ArrayList
**Difficulty:** Intermediate
**Concepts:** ArrayList, generics, encapsulation

### Problem
Implement a generic `Stack<T>` class using an `ArrayList<T>` internally. Provide: `push(T item)`, `pop()` (throws `EmptyStackException` if empty), `peek()`, `isEmpty()`, `size()`, `toString()`.

### Expected Output / Sample
```
Push: 10, 20, 30
peek() → 30
pop()  → 30
pop()  → 20
Stack: [10]
```

### Hints
- `push` → `list.add(item)`
- `pop` / `peek` → work with `list.get(list.size() - 1)` (last element is the top)

### Bonus
Add a `clear()` method and test it. Test that `pop()` on an empty stack throws an exception.

---

## Exercise 6 — Top N Scores
**Difficulty:** Intermediate
**Concepts:** ArrayList, Collections.sort(), subList

### Problem
Write a method `topN(List<Integer> scores, int n)` that returns a new list containing the `n` highest scores, sorted descending.

### Expected Output / Sample
```
Scores: [45, 92, 78, 55, 88, 95, 60, 82]
Top 3:  [95, 92, 88]
Top 5:  [95, 92, 88, 82, 78]
```

### Hints
- Sort the list descending using `Collections.sort(copy, Collections.reverseOrder())`.
- Return `copy.subList(0, n)`.

### Bonus
Return the top N as a `Set<Integer>` to also remove duplicates.

---

## Exercise 7 — Anagram Checker
**Difficulty:** Intermediate
**Concepts:** HashMap or sorting, String manipulation

### Problem
Write a method `areAnagrams(String a, String b)` that returns `true` if the two strings are anagrams of each other (same letters, same counts, different order). Case-insensitive.

### Expected Output / Sample
```
areAnagrams("listen", "silent")   → true
areAnagrams("hello", "world")     → false
areAnagrams("Triangle", "Integral") → true
```

### Hints
- Build a `Map<Character, Integer>` for each string and compare them.
- Or sort both strings as char arrays and compare with `Arrays.equals()`.

### Bonus
Write a method `groupAnagrams(List<String> words)` that groups words that are anagrams of each other: `["eat", "tea", "tan", "ate", "nat", "bat"]` → `[[eat, tea, ate], [tan, nat], [bat]]`.

---

## Exercise 8 — Phone Book
**Difficulty:** Beginner
**Concepts:** HashMap, Scanner, loop

### Problem
Build a simple phone book program using a `Map<String, String>` (name → number). Support these operations in a loop:
- `add <name> <number>` — add entry
- `lookup <name>` — print the number or "Not found"
- `remove <name>` — remove entry
- `list` — print all entries
- `quit` — exit

### Expected Output / Sample
```
> add Alice 555-1234
Added Alice.
> lookup Alice
Alice: 555-1234
> lookup Bob
Not found.
> list
Alice: 555-1234
```

### Bonus
Save the phone book to a file on quit and load it on start.

---

## Exercise 9 — Matrix Operations
**Difficulty:** Intermediate
**Concepts:** 2D arrays, nested loops

### Problem
Write methods for basic 2D matrix operations:
- `printMatrix(int[][] m)` — print formatted
- `addMatrices(int[][] a, int[][] b)` — add element-wise
- `transpose(int[][] m)` — swap rows and columns
- `multiply(int[][] a, int[][] b)` — matrix multiplication

Test with 3×3 matrices.

### Expected Output / Sample
```
Matrix A:          Transpose of A:
1 2 3              1 4 7
4 5 6              2 5 8
7 8 9              3 6 9
```

### Hints
- For transpose: `result[j][i] = original[i][j]`
- For multiply: `result[i][j] = sum of row i of A × column j of B`

### Bonus
Write a method `isDiagonal(int[][] m)` that returns true if all off-diagonal elements are zero.

---

## Exercise 10 — Inventory Manager
**Difficulty:** Intermediate
**Concepts:** HashMap, iteration, sorting

### Problem
Create an inventory system using `Map<String, Integer>` (item name → quantity). Implement:
- `addStock(String item, int qty)` — add or increase quantity
- `removeStock(String item, int qty)` — reduce quantity (throw if would go negative)
- `getLowStock(int threshold)` — return items with quantity ≤ threshold
- `printInventory()` — print all items sorted alphabetically

### Expected Output / Sample
```
Inventory:
Apple    → 50
Banana   → 3
Mango    → 12
Orange   → 2

Low stock (threshold = 5):
Banana → 3
Orange → 2
```

### Bonus
Add a `totalItems()` method that returns the sum of all quantities.

---

## Exercise 11 — Balanced Brackets
**Difficulty:** Intermediate
**Concepts:** Deque as stack, character iteration

### Problem
Write a method `isBalanced(String expression)` that returns `true` if all brackets are correctly balanced and nested. Handle `()`, `[]`, and `{}`.

### Expected Output / Sample
```
isBalanced("({[]})")    → true
isBalanced("({[}])")    → false
isBalanced("((())")     → false
isBalanced("")          → true
isBalanced("{[()]}")    → true
```

### Hints
- Push opening brackets onto a stack.
- For closing brackets, pop and check the matching opening bracket.

### Bonus
Return a helpful error message: "Mismatched bracket at position 4" rather than just `false`.

---

## Exercise 12 — Sliding Window Maximum
**Difficulty:** Advanced
**Concepts:** ArrayDeque, arrays, algorithm

### Problem
Given an array of integers and a window size `k`, find the maximum value in each sliding window of size `k`.

### Expected Output / Sample
```
Input: [1, 3, -1, -3, 5, 3, 6, 7], k = 3
Output: [3, 3, 5, 5, 6, 7]
```

### Hints
- Use an `ArrayDeque` to store indices.
- Remove indices from the front if they fall outside the window.
- Remove smaller elements from the back.

### Bonus
Also implement the sliding window minimum.

---

## Exercise 13 — Graph with Adjacency List
**Difficulty:** Advanced
**Concepts:** Map, List, BFS, Deque

### Problem
Represent an undirected graph as a `Map<String, List<String>>` (adjacency list). Implement:
- `addEdge(String from, String to)` — add undirected connection
- `getNeighbours(String node)` — return neighbour list
- `bfs(String start)` — breadth-first search, return visited nodes in order
- `isConnected(String a, String b)` — return true if a path exists

### Expected Output / Sample
```
Graph: A-B, A-C, B-D, C-D, D-E
BFS from A: [A, B, C, D, E]
isConnected(A, E): true
isConnected(A, F): false
```

### Bonus
Implement `dfs(String start)` using a stack instead of a queue.

---

## Exercise 14 — Sorted Student Roster
**Difficulty:** Intermediate
**Concepts:** TreeMap, Comparator, collections sorting

### Problem
Create a `Student` record/class with `name` and `gpa`. Build a list of 8 students. Sort and print them in three different orders:
1. Alphabetically by name (ascending)
2. By GPA descending (highest first)
3. By GPA descending, then name alphabetically as a tiebreaker

### Expected Output / Sample
```
By name: Alice (3.8), Bob (3.5), Charlie (3.8), ...
By GPA:  Alice (3.8), Charlie (3.8), Bob (3.5), ...
By GPA+name: Alice (3.8), Charlie (3.8), Bob (3.5), ...
```

### Hints
- Use `Comparator.comparing(Student::getName)` and `.reversed()`.
- Chain comparators with `.thenComparing()`.

### Bonus
Use a `TreeMap<Double, List<Student>>` to group students by GPA.

---

## Exercise 15 — LRU Cache
**Difficulty:** Advanced
**Concepts:** LinkedHashMap, encapsulation, design

### Problem
Implement an LRU (Least Recently Used) cache with a fixed capacity using `LinkedHashMap`. The cache maps `String` keys to `String` values. When the cache is full and a new entry is added, the least recently used entry is removed.

### Expected Output / Sample
```
Cache capacity: 3
put(A, 1), put(B, 2), put(C, 3)
get(A) → 1  (A is now most recently used)
put(D, 4)   (B is evicted — least recently used)
get(B) → null (evicted)
Cache: {C=3, A=1, D=4}
```

### Hints
- Extend `LinkedHashMap` with `accessOrder = true`.
- Override `removeEldestEntry()` to evict when size > capacity.

### Bonus
Make the LRU cache generic: `LRUCache<K, V>`.

---

## Exercise 16 — Set Operations: Friends Network
**Difficulty:** Intermediate
**Concepts:** HashSet, set operations

### Problem
Represent two users' friend lists as `Set<String>`. Compute:
- Mutual friends (intersection)
- Friends of Alice not known by Bob (difference)
- All people in either network (union)

### Expected Output / Sample
```
Alice's friends: {Carol, Dave, Eve, Frank}
Bob's friends:   {Dave, Eve, Grace, Henry}

Mutual friends:  {Dave, Eve}
Only Alice knows: {Carol, Frank}
Only Bob knows:   {Grace, Henry}
Full network:     {Carol, Dave, Eve, Frank, Grace, Henry}
```

### Bonus
Add a third user and find people known by all three.

---

## Exercise 17 — Priority Task Manager
**Difficulty:** Intermediate
**Concepts:** PriorityQueue, Comparable

### Problem
Create a `Task` class with `name` (String) and `priority` (int, 1 = highest). Implement `Comparable<Task>` so lower priority numbers sort first. Add 6 tasks with different priorities to a `PriorityQueue` and process (poll) them all in order.

### Expected Output / Sample
```
Processing tasks:
[Priority 1] Fix critical bug
[Priority 2] Deploy hotfix
[Priority 3] Write documentation
[Priority 4] Update tests
[Priority 5] Refactor module
[Priority 5] Code review
```

### Bonus
Add a `deadline` (LocalDate) to `Task`. Sort first by priority, then by deadline.

---

## Exercise 18 — Two-Sum Problem
**Difficulty:** Intermediate
**Concepts:** HashMap, algorithm

### Problem
Given an array of integers and a target sum, find the two numbers that add up to the target. Return their indices. Assume exactly one solution exists.

### Expected Output / Sample
```
Input: [2, 7, 11, 15], target = 9
Output: Indices [0, 1]  (2 + 7 = 9)

Input: [3, 2, 4], target = 6
Output: Indices [1, 2]  (2 + 4 = 6)
```

### Hints
- Use a `Map<Integer, Integer>` (value → index).
- For each element, check if `target - element` is already in the map.
- O(n) time — only one pass through the array.

### Bonus
Return all pairs (not just indices) if there are multiple solutions.

---

## Exercise 19 — Flatten Nested List
**Difficulty:** Intermediate
**Concepts:** List, recursion or Stack

### Problem
Given a list that can contain integers or other lists (represent as `Object` or use a wrapper), flatten it into a single list of integers.

### Expected Output / Sample
```
Input:  [1, [2, [3, 4], 5], 6, [7, 8]]
Output: [1, 2, 3, 4, 5, 6, 7, 8]
```

### Hints
- Use a `Deque<Object>` as a stack.
- If the item is an Integer, add to result. If it's a List, push all its elements onto the stack.

### Bonus
Implement recursively: if the element is a List, recurse; otherwise add to result.

---

## Exercise 20 — Collections Design: Library Catalogue
**Difficulty:** Advanced
**Concepts:** Map, Set, List, sorting, searching

### Problem
Design a library catalogue using multiple collection types:
- `Map<String, Book>` — look up book by ISBN
- `Map<String, List<Book>>` — look up books by author
- `TreeSet<Book>` (sorted by title) — browse alphabetically
- `Queue<String>` — waitlist of member names for a popular book

Implement:
- `addBook(Book book)` — adds to all three structures
- `findByIsbn(String isbn)` — returns the book
- `findByAuthor(String author)` — returns sorted list of that author's books
- `browseAlphabetical()` — prints all books sorted by title
- `joinWaitlist(String isbn, String member)` — adds to that book's waitlist
- `nextInWaitlist(String isbn)` — returns the next member in line

### Hints
- Use an inner `Map<String, Queue<String>>` for per-book waitlists.
- `Book` needs to implement `Comparable<Book>` by title for the `TreeSet`.

### Bonus
Add a search method `search(String keyword)` that returns all books whose title contains the keyword (case-insensitive).
