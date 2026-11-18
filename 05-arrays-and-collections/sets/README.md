# Sets

## What is a Set?
A `Set` stores **unique elements** — duplicates are silently ignored. If you try to add an element that is already in the set, the set remains unchanged.

**When to use a Set:**
- You need to track which items have been seen
- You need to remove duplicates from a collection
- You need fast "is this element present?" checks
- You don't care about duplicates

---

## HashSet

The fastest `Set`. No guaranteed order — elements may appear in any order.

```java
import java.util.HashSet;
import java.util.Set;

Set<String> tags = new HashSet<>();
tags.add("java");
tags.add("programming");
tags.add("java");     // duplicate — silently ignored

System.out.println(tags.size());          // 2
System.out.println(tags.contains("java")); // true

// Iteration (order not guaranteed)
for (String tag : tags) {
    System.out.println(tag);
}
```

---

## LinkedHashSet

Maintains **insertion order** — elements iterate in the order they were added.

```java
import java.util.LinkedHashSet;

Set<String> ordered = new LinkedHashSet<>();
ordered.add("banana");
ordered.add("apple");
ordered.add("mango");
ordered.add("apple");   // duplicate — ignored

for (String fruit : ordered) {
    System.out.print(fruit + " ");
}
// Output: banana apple mango  (insertion order preserved)
```

---

## TreeSet

**Sorted** in natural order (or by a `Comparator`). Operations are O(log n) instead of O(1).

```java
import java.util.TreeSet;

Set<Integer> sorted = new TreeSet<>();
sorted.add(5);
sorted.add(1);
sorted.add(3);
sorted.add(1);   // duplicate — ignored

for (int num : sorted) {
    System.out.print(num + " ");
}
// Output: 1 3 5  (sorted ascending)

// TreeSet has extra navigation methods
TreeSet<Integer> ts = new TreeSet<>(sorted);
System.out.println(ts.first());   // 1
System.out.println(ts.last());    // 5
System.out.println(ts.floor(4));  // 3 (largest element ≤ 4)
System.out.println(ts.ceiling(4)); // 5 (smallest element ≥ 4)
```

---

## Set Operations (Union, Intersection, Difference)

```java
Set<Integer> a = new HashSet<>(Set.of(1, 2, 3, 4, 5));
Set<Integer> b = new HashSet<>(Set.of(3, 4, 5, 6, 7));

// Union — all elements from both
Set<Integer> union = new HashSet<>(a);
union.addAll(b);
System.out.println(union);  // [1, 2, 3, 4, 5, 6, 7]

// Intersection — only elements in both
Set<Integer> intersection = new HashSet<>(a);
intersection.retainAll(b);
System.out.println(intersection);  // [3, 4, 5]

// Difference — elements in a but not in b
Set<Integer> difference = new HashSet<>(a);
difference.removeAll(b);
System.out.println(difference);    // [1, 2]
```

---

## Comparison Table

| | `HashSet` | `LinkedHashSet` | `TreeSet` |
|---|---|---|---|
| Order | None | Insertion order | Natural sort order |
| `add`, `contains`, `remove` | O(1) avg | O(1) avg | O(log n) |
| Null elements | 1 null allowed | 1 null allowed | ❌ No null |
| Memory | Least | Moderate | Moderate |
| Use when | Fastest unique check | Unique + ordered | Unique + sorted |

---

## Requires equals() and hashCode()

For `HashSet` and `LinkedHashSet` to correctly identify duplicates when using custom objects, those objects must properly override `equals()` and `hashCode()`. Java's `String` and `Integer` already do this.

```java
// String's equals() compares content, so this works correctly:
Set<String> set = new HashSet<>();
set.add(new String("hello"));
set.add(new String("hello"));  // same content — only one entry
System.out.println(set.size());  // 1
```

---

## Exercises

**Exercise 1:** Create a `List<String>` with duplicate names. Use a `LinkedHashSet` to remove duplicates while maintaining order. Print before and after.

**Exercise 2:** Given two `Set<Integer>` objects, compute and print their union, intersection, and difference.

**Exercise 3:** Write a method `findDuplicates(List<String> list)` that returns a `Set<String>` of all elements that appear more than once. Test with `["a", "b", "a", "c", "b", "d"]` → `{a, b}`.
