# break and continue

## Overview
`break` and `continue` give you fine-grained control over loop execution. They let you exit a loop early or skip individual iterations without changing the loop condition.

---

## break
`break` immediately exits the current loop (or `switch` block). Execution continues with the first statement after the loop.

```java
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break;  // stops the loop when i reaches 5
    }
    System.out.println(i);
}
// Output: 1  2  3  4
// (5 and above are never printed)
```

```java
// break in a while loop
int attempts = 0;
while (true) {          // intentionally infinite
    attempts++;
    System.out.println("Attempt " + attempts);
    if (attempts >= 3) {
        break;          // exit when we've had enough
    }
}
System.out.println("Done after " + attempts + " attempts.");
```

---

## continue
`continue` skips the rest of the current iteration and jumps straight to the next one.

```java
for (int i = 1; i <= 10; i++) {
    if (i % 2 == 0) {
        continue;  // skip even numbers
    }
    System.out.println(i);
}
// Output: 1  3  5  7  9
```

```java
// Skip a specific value in a list
String[] names = {"Alice", "", "Bob", null, "Charlie"};
for (String name : names) {
    if (name == null || name.isEmpty()) {
        continue;  // skip blank/null entries
    }
    System.out.println("Hello, " + name + "!");
}
```

---

## Labelled break (for nested loops)
A plain `break` only exits the innermost loop. If you need to exit multiple nested loops at once, use a **label**.

```java
outer:                               // label on the outer loop
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) {
            break outer;             // breaks out of BOTH loops
        }
        System.out.println(i + "," + j);
    }
}
// Output:
// 0,0
// 0,1
// 0,2
// 1,0
// (stops at i=1, j=1)
```

---

## Labelled continue
Similarly, `continue outerLabel` skips to the next iteration of the outer loop:

```java
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) {
            continue outer;  // skip rest of inner loop, next i
        }
        System.out.println(i + "," + j);
    }
}
// Output:
// 0,0
// 1,0
// 2,0
```

---

## Practical Example — Search with break
```java
String[] names = {"Alice", "Bob", "Charlie", "Diana"};
String target = "Charlie";
boolean found = false;

for (String name : names) {
    if (name.equals(target)) {
        found = true;
        break;  // no need to keep searching
    }
}

System.out.println(found ? "Found!" : "Not found.");
// Output: Found!
```

Without `break`, the loop would continue checking `"Diana"` even after already finding the target — wasted work.

---

## When to Use Each

| Keyword | Use when |
|---------|----------|
| `break` | You've found what you need, or a terminating condition is met mid-loop |
| `continue` | You want to skip a specific element but continue processing the rest |
| `break label` | You need to exit multiple nested loops at once |
| `continue label` | You need to skip to the next iteration of an outer loop |

---

## Common Mistakes
- Using `break` inside an `if` statement that is not inside a loop — that is a compile error.
- Forgetting that `break` only exits the **innermost** loop — use a label if you need to go further out.
- Over-using `break` and `continue` where a better loop condition would be clearer.

---

## Exercises

**Exercise 1:** Print numbers from 1 to 20. Use `continue` to skip multiples of 3.

**Exercise 2:** Write a loop that searches an integer array for the value `42`. Use `break` when found and print its index. Print "Not found" if it isn't there.

**Exercise 3:** Write a nested loop that prints coordinate pairs (i, j) for i from 0 to 4 and j from 0 to 4. Use a labelled `break` to stop everything as soon as `i + j == 6`.
