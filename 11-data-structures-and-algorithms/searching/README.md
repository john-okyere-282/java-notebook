# Searching Algorithms

## Linear Search

### Algorithm

Check each element one by one from the start until the target is found or the array is exhausted.

```
For each element in array:
    If element == target → return its index
Return -1 (not found)
```

### Java Implementation

```java
public class LinearSearch {

    /**
     * Returns the index of target in array, or -1 if not found.
     * Time complexity:  O(n)
     * Space complexity: O(1)
     */
    public static int search(int[] array, int target) {
        for (int i = 0; i < array.length; i++) {
            if (array[i] == target) {
                return i;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        int[] numbers = {64, 25, 12, 22, 11};

        int index = search(numbers, 22);
        System.out.println("Found at index: " + index); // 3

        int notFound = search(numbers, 99);
        System.out.println("Found at index: " + notFound); // -1
    }
}
```

### Complexity

| Case | Time | Space |
|------|------|-------|
| Best | O(1) — target is first element | O(1) |
| Average | O(n/2) = O(n) | O(1) |
| Worst | O(n) — target is last or absent | O(1) |

### Generic version

```java
public static <T> int search(T[] array, T target) {
    for (int i = 0; i < array.length; i++) {
        if (array[i].equals(target)) {
            return i;
        }
    }
    return -1;
}
```

---

## Binary Search

### Requirement

**The array must be sorted.** Binary search is undefined on unsorted data.

### Algorithm

Maintain a search window (`low` to `high`). At each step, compare the middle element with the target:
- If equal → found, return index.
- If target is less → search the left half.
- If target is greater → search the right half.
- If `low > high` → not found.

```
low = 0, high = n - 1

While low <= high:
    mid = (low + high) / 2
    If array[mid] == target → return mid
    If array[mid] < target  → low = mid + 1
    If array[mid] > target  → high = mid - 1

Return -1
```

### Java Implementation (Iterative)

```java
public class BinarySearch {

    /**
     * Returns the index of target in a sorted array, or -1 if not found.
     * Time complexity:  O(log n)
     * Space complexity: O(1)
     */
    public static int search(int[] sortedArray, int target) {
        int low  = 0;
        int high = sortedArray.length - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;  // avoids integer overflow vs (low+high)/2

            if (sortedArray[mid] == target) {
                return mid;
            } else if (sortedArray[mid] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return -1;
    }

    public static void main(String[] args) {
        int[] sorted = {2, 5, 8, 12, 16, 23, 38, 56, 72, 91};

        System.out.println(search(sorted, 23));  // 5
        System.out.println(search(sorted, 99));  // -1
    }
}
```

### Java Implementation (Recursive)

```java
public static int searchRecursive(int[] array, int target, int low, int high) {
    if (low > high) return -1;

    int mid = low + (high - low) / 2;

    if (array[mid] == target)       return mid;
    if (array[mid] < target)        return searchRecursive(array, target, mid + 1, high);
    return searchRecursive(array, target, low, mid - 1);
}
```

Recursive binary search uses O(log n) stack space. The iterative version is O(1) space.

### Complexity

| Case | Time | Space |
|------|------|-------|
| Best | O(1) — target is the middle element | O(1) |
| Average | O(log n) | O(1) |
| Worst | O(log n) | O(1) |

### Why O(log n)?

An array of 1,000,000 elements requires at most **20 comparisons** (log₂ 1,000,000 ≈ 20). Each step halves the remaining search space.

---

## Arrays.binarySearch() — Built-in Utility

Java's standard library provides `Arrays.binarySearch()`. Use this in production instead of writing your own.

```java
import java.util.Arrays;

int[] sorted = {2, 5, 8, 12, 16, 23, 38, 56, 72, 91};

int index = Arrays.binarySearch(sorted, 23);
System.out.println(index); // 5

// When not found, returns -(insertion point) - 1
int missing = Arrays.binarySearch(sorted, 10);
System.out.println(missing); // negative — 10 would go between index 2 and 3
```

> **Precondition:** The array must be sorted before calling `Arrays.binarySearch()`. Results are undefined on unsorted arrays.

For `List`, use `Collections.binarySearch()`:

```java
import java.util.Collections;
import java.util.List;

List<Integer> list = List.of(2, 5, 8, 12, 16, 23);
int index = Collections.binarySearch(list, 12); // 3
```

---

## When to Use Each

| Algorithm | Use when |
|-----------|---------|
| Linear search | Data is unsorted; dataset is small; searching a `LinkedList` where random access is O(n) anyway |
| Binary search | Data is sorted (or can be sorted); dataset is large and searched repeatedly |

**Rule of thumb:** If you search the same sorted dataset many times, the O(n log n) sort cost amortises over O(log n) searches. If you search only once, linear search is simpler.
