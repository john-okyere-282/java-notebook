# Arrays

## What is an Array?
An array is a **fixed-size, ordered collection of elements of the same type**. Once created, the size cannot change. Arrays are the most fundamental data structure in Java — fast, memory-efficient, and work with primitive types.

---

## Declaring and Creating Arrays

```java
// Declaration only (no memory allocated yet)
int[] scores;

// Declaration + create (all elements initialised to 0)
int[] scores = new int[5];

// Declaration + create + initialise with values
String[] names = {"Alice", "Bob", "Charlie"};

// Two-step: declare then create
int[] numbers;
numbers = new int[10];
```

Default values after `new int[5]`:
| Type | Default |
|------|---------|
| `int`, `long`, `short`, `byte` | `0` |
| `double`, `float` | `0.0` |
| `boolean` | `false` |
| `String`, any object | `null` |

---

## Accessing Elements

Arrays are **zero-indexed** — the first element is at index 0.

```java
String[] names = {"Alice", "Bob", "Charlie"};

System.out.println(names[0]);   // Alice
System.out.println(names[2]);   // Charlie
System.out.println(names.length);  // 3 (not 2!)

names[1] = "Bob Smith";         // update an element
// names[5] = "Dave";           // ArrayIndexOutOfBoundsException — index 5 doesn't exist
```

---

## Iterating Over Arrays

```java
int[] scores = {85, 92, 78, 95, 88};

// for loop — use when you need the index
for (int i = 0; i < scores.length; i++) {
    System.out.println("scores[" + i + "] = " + scores[i]);
}

// for-each — use when you only need values
for (int score : scores) {
    System.out.println(score);
}
```

---

## Multidimensional Arrays (2D)

A 2D array is an array of arrays.

```java
int[][] matrix = new int[3][3];           // 3 rows, 3 columns
int[][] grid   = { {1, 2, 3},
                   {4, 5, 6},
                   {7, 8, 9} };

// Access: grid[row][column]
System.out.println(grid[0][0]);  // 1 (top-left)
System.out.println(grid[1][2]);  // 6 (row 1, column 2)
System.out.println(grid[2][2]);  // 9 (bottom-right)

// Traverse with nested loops
for (int[] row : grid) {
    for (int val : row) {
        System.out.printf("%4d", val);
    }
    System.out.println();
}
// Output:
//    1   2   3
//    4   5   6
//    7   8   9
```

---

## Arrays Utility Class

`java.util.Arrays` provides essential helper methods:

```java
import java.util.Arrays;

int[] nums = {5, 2, 8, 1, 9, 3};

// Sort in ascending order (modifies the original array)
Arrays.sort(nums);
System.out.println(Arrays.toString(nums));   // [1, 2, 3, 5, 8, 9]

// Binary search (array MUST be sorted first)
int index = Arrays.binarySearch(nums, 8);
System.out.println("Found 8 at index: " + index);  // 4

// Copy — creates a new array with the first N elements
int[] first4 = Arrays.copyOf(nums, 4);
System.out.println(Arrays.toString(first4));  // [1, 2, 3, 5]

// Copy a range
int[] middle = Arrays.copyOfRange(nums, 1, 4);
System.out.println(Arrays.toString(middle));  // [2, 3, 5]

// Fill all elements with a value
int[] zeros = new int[5];
Arrays.fill(zeros, 7);
System.out.println(Arrays.toString(zeros));   // [7, 7, 7, 7, 7]

// Equality check
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};
System.out.println(Arrays.equals(a, b));      // true (a == b would be false)
```

---

## Arrays vs ArrayList

| | Array | ArrayList |
|---|---|---|
| Size | Fixed at creation | Dynamic (grows/shrinks) |
| Primitives | ✅ Yes (`int[]`, `double[]`) | ❌ No (use `Integer`, `Double`) |
| Syntax | `int[]`, `new int[5]` | `new ArrayList<Integer>()` |
| Performance | Slightly faster | Slightly more overhead |
| Utility methods | `Arrays.sort()`, etc. | `list.sort()`, `Collections.sort()` |
| When to use | Known size, working with primitives | Unknown size, need add/remove |

---

## Exercises

**Exercise 1:** Create an `int` array of 10 elements, fill it with the squares of 1–10, then print it using `Arrays.toString()`.

**Exercise 2:** Write a method `findMax(int[] array)` that finds and returns the maximum value without using `Arrays.sort()`. Test it.

**Exercise 3:** Write a method `reverse(int[] array)` that reverses an array in-place (without creating a new array). Print before and after.

**Exercise 4:** Create a 3×3 2D array representing a tic-tac-toe board. Fill it with `'X'` and `'O'`. Write a method `printBoard(char[][] board)` that prints it nicely.

**Exercise 5:** Write a method `merge(int[] a, int[] b)` that creates and returns a new array containing all elements of `a` followed by all elements of `b`. Sort the result.
