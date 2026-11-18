# ArrayList

## What is ArrayList?
`ArrayList` is a **resizable array**. It grows and shrinks automatically as you add and remove elements. It is the most commonly used collection in Java.

Located in `java.util`. Implements the `List` interface.

---

## Basic Usage

```java
import java.util.ArrayList;
import java.util.List;

// Always declare with the interface type (List), not the concrete type (ArrayList)
List<String> fruits = new ArrayList<>();

// Add elements
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Mango");

System.out.println(fruits);           // [Apple, Banana, Mango]
System.out.println(fruits.size());    // 3
System.out.println(fruits.get(1));    // Banana   (0-indexed)

// Update an element
fruits.set(1, "Strawberry");
System.out.println(fruits);           // [Apple, Strawberry, Mango]

// Remove by value
fruits.remove("Apple");
System.out.println(fruits);           // [Strawberry, Mango]

// Remove by index
fruits.remove(0);
System.out.println(fruits);           // [Mango]
```

---

## Common Methods

| Method | What it does |
|--------|-------------|
| `add(E element)` | Appends to the end |
| `add(int index, E element)` | Inserts at position (shifts others) |
| `get(int index)` | Returns element at index |
| `set(int index, E element)` | Replaces element at index |
| `remove(int index)` | Removes by index |
| `remove(Object o)` | Removes first occurrence of value |
| `size()` | Number of elements |
| `isEmpty()` | True if size is 0 |
| `contains(Object o)` | True if element is in the list |
| `indexOf(Object o)` | First index of element (-1 if not found) |
| `clear()` | Removes all elements |
| `sort(Comparator)` | Sorts in-place |
| `subList(from, to)` | View of a portion |
| `toArray()` | Converts to an array |
| `addAll(Collection c)` | Appends all elements from another collection |

---

## Iterating Over an ArrayList

```java
List<String> colours = new ArrayList<>(List.of("Red", "Green", "Blue", "Yellow"));

// for-each (most common)
for (String colour : colours) {
    System.out.println(colour);
}

// classic for loop (when you need the index)
for (int i = 0; i < colours.size(); i++) {
    System.out.println(i + ": " + colours.get(i));
}

// forEach with lambda (Java 8+)
colours.forEach(colour -> System.out.println(colour.toUpperCase()));
```

---

## Sorting

```java
import java.util.Collections;

List<Integer> numbers = new ArrayList<>(List.of(5, 2, 8, 1, 9, 3));

// Natural order (ascending)
Collections.sort(numbers);
System.out.println(numbers);  // [1, 2, 3, 5, 8, 9]

// Reverse order
numbers.sort(Collections.reverseOrder());
System.out.println(numbers);  // [9, 8, 5, 3, 2, 1]

// Sort by string length
List<String> words = new ArrayList<>(List.of("banana", "kiwi", "mango", "fig"));
words.sort((a, b) -> a.length() - b.length());
System.out.println(words);  // [fig, kiwi, mango, banana]
```

---

## Immutable Lists (Java 9+)

```java
// List.of() creates an unmodifiable list
List<String> immutable = List.of("A", "B", "C");
// immutable.add("D");  // throws UnsupportedOperationException

// To get a mutable copy from an immutable one:
List<String> mutable = new ArrayList<>(List.of("A", "B", "C"));
mutable.add("D");  // OK
```

---

## ArrayList vs LinkedList

Both implement `List`, but their internal structures differ:

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| `get(i)` | ⚡ O(1) — direct index | 🐢 O(n) — must traverse |
| `add` at end | ⚡ O(1) amortized | ⚡ O(1) |
| `add` at beginning/middle | 🐢 O(n) — shifts elements | ⚡ O(1) if you have the node |
| `remove` at beginning/middle | 🐢 O(n) | ⚡ O(1) if you have the node |
| Memory | Less (compact) | More (node + two pointers per element) |
| **Best for** | Random access, append | Frequent insert/remove at start |

**In practice:** Use `ArrayList` by default. Use `LinkedList` only if you are doing many insertions/deletions at the beginning or middle and profiling confirms it matters.

---

## Exercises

**Exercise 1:** Create an `ArrayList<String>` of five country names. Add two more. Remove one by name. Print the final list.

**Exercise 2:** Create an `ArrayList<Integer>` of 10 numbers. Use `Collections.sort()` to sort ascending, then print the minimum, maximum, and sum.

**Exercise 3:** Write a method `removeDuplicates(List<String> list)` that returns a new list with duplicates removed (maintain original order).

**Exercise 4:** Create an `ArrayList<Integer>` from 1 to 20. Remove all even numbers using `removeIf()`. Print the result.

**Exercise 5:** Write a method `topN(List<Integer> numbers, int n)` that returns a new list containing the `n` highest numbers, sorted descending.
