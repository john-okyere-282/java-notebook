# Sorting Algorithms

## Bubble Sort

### How It Works

Repeatedly steps through the list, compares adjacent pairs, and swaps them if they are out of order. After each pass, the largest unsorted element "bubbles" to its correct position at the end.

### Java Implementation

```java
public class BubbleSort {

    public static void sort(int[] array) {
        int n = array.length;
        for (int i = 0; i < n - 1; i++) {
            boolean swapped = false;
            for (int j = 0; j < n - i - 1; j++) {
                if (array[j] > array[j + 1]) {
                    int temp    = array[j];
                    array[j]    = array[j + 1];
                    array[j + 1] = temp;
                    swapped = true;
                }
            }
            if (!swapped) break; // already sorted — optimisation
        }
    }
}
```

**Complexity:** O(n²) time, O(1) space. Stable sort. Use only for teaching — never in production.

---

## Selection Sort

### How It Works

Divides the array into a sorted and unsorted portion. Finds the minimum element in the unsorted portion and swaps it into the next sorted position.

### Java Implementation

```java
public class SelectionSort {

    public static void sort(int[] array) {
        int n = array.length;
        for (int i = 0; i < n - 1; i++) {
            int minIndex = i;
            for (int j = i + 1; j < n; j++) {
                if (array[j] < array[minIndex]) {
                    minIndex = j;
                }
            }
            // Swap array[i] with array[minIndex]
            int temp        = array[minIndex];
            array[minIndex] = array[i];
            array[i]        = temp;
        }
    }
}
```

**Complexity:** O(n²) time, O(1) space. Not stable. Performs fewer swaps than bubble sort.

---

## Insertion Sort

### How It Works

Builds the sorted array one element at a time. Takes each new element and inserts it into its correct position among the already-sorted elements by shifting larger elements right.

### Java Implementation

```java
public class InsertionSort {

    public static void sort(int[] array) {
        int n = array.length;
        for (int i = 1; i < n; i++) {
            int key = array[i];
            int j   = i - 1;
            while (j >= 0 && array[j] > key) {
                array[j + 1] = array[j];
                j--;
            }
            array[j + 1] = key;
        }
    }
}
```

**Complexity:** O(n²) time, O(1) space. Stable. Efficient for nearly-sorted data and small arrays. Used as the base case in many hybrid sorts.

---

## Merge Sort

### How It Works

Divide-and-conquer: splits the array in half recursively until single elements, then merges pairs of sorted halves back together.

```
[38, 27, 43, 3, 9, 82]
→ [38, 27, 43] | [3, 9, 82]
→ [38, 27] [43] | [3, 9] [82]
→ [38] [27] [43] | [3] [9] [82]
← [27, 38] [43] | [3, 9] [82]
← [27, 38, 43] | [3, 9, 82]
← [3, 9, 27, 38, 43, 82]
```

### Java Implementation

```java
public class MergeSort {

    public static void sort(int[] array) {
        if (array.length <= 1) return;
        mergeSort(array, 0, array.length - 1);
    }

    private static void mergeSort(int[] array, int left, int right) {
        if (left >= right) return;

        int mid = left + (right - left) / 2;
        mergeSort(array, left, mid);
        mergeSort(array, mid + 1, right);
        merge(array, left, mid, right);
    }

    private static void merge(int[] array, int left, int mid, int right) {
        int n1 = mid - left + 1;
        int n2 = right - mid;

        int[] leftArr  = new int[n1];
        int[] rightArr = new int[n2];

        System.arraycopy(array, left,     leftArr,  0, n1);
        System.arraycopy(array, mid + 1,  rightArr, 0, n2);

        int i = 0, j = 0, k = left;
        while (i < n1 && j < n2) {
            if (leftArr[i] <= rightArr[j]) {
                array[k++] = leftArr[i++];
            } else {
                array[k++] = rightArr[j++];
            }
        }
        while (i < n1) array[k++] = leftArr[i++];
        while (j < n2) array[k++] = rightArr[j++];
    }
}
```

**Complexity:** O(n log n) time, O(n) space. Stable. Preferred for sorting linked lists. Predictable performance — no worst-case degradation.

---

## Quick Sort

### How It Works

Divide-and-conquer: picks a "pivot" element, partitions the array so all elements smaller than the pivot are to its left and all larger are to its right, then recursively sorts each half. The pivot is in its final position after partitioning.

### Java Implementation

```java
public class QuickSort {

    public static void sort(int[] array) {
        quickSort(array, 0, array.length - 1);
    }

    private static void quickSort(int[] array, int low, int high) {
        if (low < high) {
            int pivotIndex = partition(array, low, high);
            quickSort(array, low, pivotIndex - 1);
            quickSort(array, pivotIndex + 1, high);
        }
    }

    // Lomuto partition scheme — pivot is the last element
    private static int partition(int[] array, int low, int high) {
        int pivot = array[high];
        int i     = low - 1;

        for (int j = low; j < high; j++) {
            if (array[j] <= pivot) {
                i++;
                int temp  = array[i];
                array[i]  = array[j];
                array[j]  = temp;
            }
        }
        // Place pivot in its correct position
        int temp       = array[i + 1];
        array[i + 1]   = array[high];
        array[high]    = temp;

        return i + 1;
    }
}
```

**Complexity:** O(n log n) average, O(n²) worst case (already-sorted input with naive pivot). O(log n) space (recursion stack). Not stable. Very cache-friendly — fast in practice.

---

## Java's Built-in Sort — Arrays.sort() and Collections.sort()

**Always use the built-in sort in production code.** It uses TimSort (a hybrid of merge sort and insertion sort) which:
- Runs in O(n log n) in all cases.
- Is stable.
- Exploits existing order in real-world data for near-O(n) performance on nearly-sorted arrays.

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.Comparator;

// Sort a primitive array
int[] numbers = {38, 27, 43, 3, 9};
Arrays.sort(numbers);

// Sort an object array
String[] names = {"Charlie", "Alice", "Bob"};
Arrays.sort(names);  // natural order

// Sort a List
List<String> list = new ArrayList<>(List.of("Charlie", "Alice", "Bob"));
Collections.sort(list);

// Sort with a custom Comparator (sort by length, then alphabetically)
list.sort(Comparator.comparingInt(String::length).thenComparing(Comparator.naturalOrder()));

// Sort a List of custom objects
List<Employee> employees = ...;
employees.sort(Comparator.comparing(Employee::getLastName));
```

---

## Comparison Table

| Algorithm | Time (avg) | Time (worst) | Space | Stable | Notes |
|-----------|-----------|-------------|-------|--------|-------|
| Bubble Sort | O(n²) | O(n²) | O(1) | ✅ | Avoid; for teaching only |
| Selection Sort | O(n²) | O(n²) | O(1) | ❌ | Minimal swaps; avoid in production |
| Insertion Sort | O(n²) | O(n²) | O(1) | ✅ | Good for small/nearly-sorted arrays |
| Merge Sort | O(n log n) | O(n log n) | O(n) | ✅ | Preferred for linked lists; predictable |
| Quick Sort | O(n log n) | O(n²) | O(log n) | ❌ | Fastest in practice on arrays |
| **TimSort (Java)** | **O(n log n)** | **O(n log n)** | **O(n)** | **✅** | **Use this** |

---

## When to Write Your Own Sort

Almost never. Use `Arrays.sort()` or `Collections.sort()` with a `Comparator`.

Write your own when:
- You are learning DSA (the whole point of this phase).
- You have a highly specialised case where a counting sort or radix sort (non-comparison sorts) would be O(n).
- You are implementing a sorting algorithm as part of an interview or exercise.
