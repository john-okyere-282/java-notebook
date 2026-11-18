# Recursion

## What is Recursion?
A recursive method is one that **calls itself**. Each call must move towards a stopping condition — the **base case**. Without a base case, recursion never stops and the program crashes with a `StackOverflowError`.

**Real-world analogy:** Russian nesting dolls. To get to the innermost doll, you open the outer one, then the next, then the next — until you reach the smallest one that cannot be opened. That smallest doll is the base case. Then you stop.

---

## Anatomy of a Recursive Method

Every recursive method needs exactly two things:
1. **Base case:** a condition where the method stops and returns directly.
2. **Recursive case:** the method calls itself with a **smaller** version of the problem.

```java
public static int factorial(int n) {
    // Base case: stop recursion
    if (n == 0 || n == 1) {
        return 1;
    }
    // Recursive case: call itself with a smaller problem
    return n * factorial(n - 1);
}
```

---

## Call Stack Trace — factorial(4)

```
factorial(4)
  → 4 * factorial(3)
         → 3 * factorial(2)
                → 2 * factorial(1)
                       → return 1          ← base case reached
                return 2 * 1 = 2
         return 3 * 2 = 6
  return 4 * 6 = 24
```

Each call waits while the next one runs. When the base case returns, the results unwind back up.

---

## Fibonacci

```java
public static int fibonacci(int n) {
    if (n <= 1) return n;              // base case: fib(0) = 0, fib(1) = 1
    return fibonacci(n - 1) + fibonacci(n - 2);  // recursive case
}

// fibonacci(0) = 0
// fibonacci(1) = 1
// fibonacci(5) = 5
// fibonacci(10) = 55
```

> ⚠️ **Warning:** This recursive Fibonacci is easy to understand but very slow for large `n` — it recalculates the same values many times. `fibonacci(50)` will take seconds. For large values, use a loop or memoisation.

---

## Stack Overflow

If there is no base case, or the base case is never reached, the call stack fills up:

```java
public static int infinite(int n) {
    return infinite(n - 1);  // never stops!
    // Throws: java.lang.StackOverflowError
}
```

The call stack has limited space. Each method call takes up a **stack frame**. Too many nested calls and you run out.

---

## Power Function

```java
public static long power(int base, int exponent) {
    if (exponent == 0) return 1;             // base case: anything^0 = 1
    return base * power(base, exponent - 1); // recursive case
}

// power(2, 10) = 1024
```

---

## Sum of Digits (Recursive)

```java
public static int sumDigits(int n) {
    if (n < 10) return n;                        // base case: single digit
    return (n % 10) + sumDigits(n / 10);         // last digit + rest
}

// sumDigits(123) = 3 + sumDigits(12)
//                     = 3 + 2 + sumDigits(1)
//                               = 3 + 2 + 1 = 6
```

---

## When to Use Recursion

**Good candidates for recursion:**
- Tree traversal (file systems, HTML DOM, organisation charts)
- Nested data structures (JSON parsing)
- Divide-and-conquer algorithms (merge sort, binary search)
- Mathematical definitions that are recursive by nature (factorial, Fibonacci)

**When NOT to use recursion:**
- Simple counting loops (use a `for` loop — clearer and faster)
- When the depth could be very large (risk of `StackOverflowError`)
- When an iterative solution is just as readable

---

## Recursion vs Iteration — Factorial

```java
// Recursive
public static long factorialRecursive(int n) {
    if (n <= 1) return 1;
    return n * factorialRecursive(n - 1);
}

// Iterative — clearer for simple cases
public static long factorialIterative(int n) {
    long result = 1;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}
```

Both give the same answer. For factorial, the iterative version is usually preferred in production code — simpler and no risk of stack overflow.

---

## Exercises

**Exercise 1:** Write a recursive method `countdown(int n)` that prints from `n` down to `0` and then prints "Go!". The base case is `n < 0`.

**Exercise 2:** Write a recursive method `sumTo(int n)` that returns `1 + 2 + 3 + ... + n`. Verify: `sumTo(10)` = 55.

**Exercise 3:** Write a recursive method `reverseString(String s)` that returns the reverse. Base case: empty or single character string.

**Exercise 4:** Write a recursive method `power(int base, int exponent)` that returns `base` raised to `exponent`. Do not use `Math.pow()`. Handle the base case `exponent == 0`.

**Exercise 5:** Write a recursive method `fibonacci(int n)`. Then write an iterative version of the same function. Compare the two and explain the trade-offs in a comment.
