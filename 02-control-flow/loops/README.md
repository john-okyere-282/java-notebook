# Loops

## What is a Loop?
A loop runs a block of code repeatedly until a condition is no longer true.

**Real-world analogy:** A printer printing 50 copies does the same action 50 times. You tell it once what to do and how many times. Without loops, you would have to write `System.out.println(...)` 50 times.

---

## The for Loop
Best when you know exactly how many times to repeat.

```java
// Syntax: for (initializer; condition; update)
for (int i = 1; i <= 5; i++) {
    System.out.println("Count: " + i);
}
// Output: Count: 1, Count: 2, Count: 3, Count: 4, Count: 5
```

**Annotated parts:**
| Part | Runs when | Purpose |
|------|-----------|---------|
| `int i = 1` (initializer) | Once, before the loop starts | Sets up the counter variable |
| `i <= 5` (condition) | Before each iteration | If false, loop ends |
| `i++` (update) | After each iteration | Moves the counter forward |

```java
// Counting down
for (int i = 10; i >= 1; i--) {
    System.out.print(i + " ");
}
System.out.println("Go!");

// Loop with a step of 2
for (int i = 0; i <= 20; i += 2) {
    System.out.print(i + " ");  // 0 2 4 6 8 10 12 14 16 18 20
}
```

---

## The while Loop
Best when you repeat until a condition becomes false and you don't know in advance how many times.

```java
int count = 1;
while (count <= 5) {
    System.out.println("Count: " + count);
    count++;  // MUST update or loop runs forever
}
```

```java
// Real use: keep asking until valid input
Scanner scanner = new Scanner(System.in);
System.out.print("Enter a positive number: ");
int number = scanner.nextInt();

while (number <= 0) {
    System.out.print("That's not positive. Try again: ");
    number = scanner.nextInt();
}
System.out.println("You entered: " + number);
```

---

## The do-while Loop
Like `while`, but the body always runs **at least once** — the condition is checked after the body.

```java
Scanner scanner = new Scanner(System.in);
int choice;

do {
    System.out.print("Enter a number between 1 and 10: ");
    choice = scanner.nextInt();
} while (choice < 1 || choice > 10);

System.out.println("Valid input: " + choice);
```

The body runs first, then Java checks whether to continue. Use this pattern when you need to run the body at least once regardless of the condition (e.g. displaying a menu before checking input).

---

## The Enhanced for-each Loop
For iterating over arrays and collections without an index variable:

```java
String[] fruits = {"apple", "banana", "mango"};
for (String fruit : fruits) {
    System.out.println(fruit);
}
// Read as: "for each String named fruit in fruits"
```

```java
List<Integer> numbers = List.of(10, 20, 30, 40);
int total = 0;
for (int num : numbers) {
    total += num;
}
System.out.println("Total: " + total);  // 100
```

> **Limitation:** You cannot modify the collection while iterating with for-each, and you don't have access to the index. Use a regular `for` loop when you need either of those.

---

## Nested Loops
A loop inside another loop. The inner loop completes fully for each iteration of the outer loop.

```java
// Multiplication table (3x3)
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        System.out.printf("%4d", i * j);
    }
    System.out.println();
}
// Output:
//    1   2   3
//    2   4   6
//    3   6   9
```

```java
// Star pattern
for (int row = 1; row <= 4; row++) {
    for (int col = 1; col <= row; col++) {
        System.out.print("* ");
    }
    System.out.println();
}
// Output:
// *
// * *
// * * *
// * * * *
```

---

## Choosing the Right Loop

| Situation | Use |
|-----------|-----|
| Known number of iterations | `for` |
| Unknown iterations, check condition first | `while` |
| Must run at least once | `do-while` |
| Iterate over all elements of an array/collection | `for-each` |

---

## Common Mistakes

1. **Infinite loop:** forgetting to update the loop variable.
   ```java
   int i = 0;
   while (i < 5) {
       System.out.println(i);
       // forgot i++ — loops forever!
   }
   ```

2. **Off-by-one error:** using `<` vs `<=` incorrectly.
   ```java
   // Prints 0 to 4 (5 numbers), NOT 1 to 5
   for (int i = 0; i < 5; i++) { ... }

   // Prints 1 to 5
   for (int i = 1; i <= 5; i++) { ... }
   ```

3. **Modifying a collection during for-each** — throws `ConcurrentModificationException`. Use an Iterator or loop by index instead.

4. **Nested loop variable reuse** — declare the inner loop variable inside the inner loop so it resets properly.

---

## Exercises

**Exercise 1:** Print all numbers from 1 to 100 using a `for` loop.

**Exercise 2:** Use a `while` loop to calculate the sum of all digits of a number entered by the user (e.g. 1234 → 10).

**Exercise 3:** Use a `do-while` loop to keep asking the user for a password until they type `"secret"`.

**Exercise 4:** Print the even numbers between 1 and 50 using a `for` loop with a step of 2.

**Exercise 5:** Print a 5×5 multiplication table using nested `for` loops.
