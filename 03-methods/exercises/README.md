# Phase 03 — Methods Exercises

Practice exercises covering method creation, parameters, return types, overloading, scope, static vs instance, and recursion.

---

## Exercise 1 — isEven
**Difficulty:** Beginner
**Concepts:** method, boolean return type, modulus

### Problem
Write a method `isEven(int n)` that returns `true` if `n` is even and `false` otherwise. Test it in `main()` for the numbers -4, 0, 7, 100.

### Expected Output / Sample
```
isEven(-4): true
isEven(0): true
isEven(7): false
isEven(100): true
```

### Hints
- Use `n % 2 == 0`.

### Bonus
Write a companion method `isOdd(int n)` that calls `isEven()` rather than duplicating the logic.

---

## Exercise 2 — Max of Three
**Difficulty:** Beginner
**Concepts:** method, parameters, if-else

### Problem
Write a method `max(int a, int b, int c)` that returns the largest of the three numbers. Test it with at least four different sets of values, including ties.

### Expected Output / Sample
```
max(3, 7, 2) = 7
max(5, 5, 5) = 5
max(-1, -3, -2) = -1
```

### Hints
- Compare two at a time.

### Bonus
Write an overloaded version `max(int a, int b)` for two values.

---

## Exercise 3 — repeatString
**Difficulty:** Beginner
**Concepts:** method, String, loop inside a method

### Problem
Write a method `repeatString(String s, int times)` that returns the string repeated `times` times. For example, `repeatString("ha", 3)` returns `"hahaha"`.

### Expected Output / Sample
```
repeatString("ha", 3)  → hahaha
repeatString("ab", 1)  → ab
repeatString("x", 5)   → xxxxx
```

### Hints
- Use a `StringBuilder` and append in a loop, or use `String.repeat(times)` (Java 11+).

### Bonus
Add validation: if `times <= 0`, return an empty string.

---

## Exercise 4 — isPalindrome
**Difficulty:** Intermediate
**Concepts:** method, String manipulation, boolean

### Problem
Write a method `isPalindrome(String s)` that returns `true` if the string reads the same forwards and backwards, ignoring case. For example: `"racecar"` → `true`, `"Level"` → `true`, `"hello"` → `false`.

### Expected Output / Sample
```
isPalindrome("racecar") → true
isPalindrome("Level")   → true
isPalindrome("hello")   → false
isPalindrome("A")       → true
```

### Hints
- Reverse the string, convert both to lowercase, and compare with `.equals()`.
- Or use two pointers (index from start and end, moving inward).

### Bonus
Extend it to ignore spaces and punctuation: `"A man a plan a canal Panama"` → `true`.

---

## Exercise 5 — sumDigits (Recursive)
**Difficulty:** Intermediate
**Concepts:** recursion, modulus, integer division

### Problem
Write a recursive method `sumDigits(int n)` that returns the sum of all digits in `n`. For example, `sumDigits(123)` returns `6` (1 + 2 + 3).

### Expected Output / Sample
```
sumDigits(123)   → 6
sumDigits(999)   → 27
sumDigits(1000)  → 1
sumDigits(7)     → 7
```

### Hints
- Base case: a single digit (n < 10) — return it directly.
- Recursive case: `(n % 10) + sumDigits(n / 10)`.

### Bonus
Write an iterative version and verify both give the same results.

---

## Exercise 6 — Overloaded area()
**Difficulty:** Intermediate
**Concepts:** method overloading, Math.PI

### Problem
Write three overloaded `area()` methods in the same class:
1. `area(double radius)` — area of a circle: `π × r²`
2. `area(double width, double height)` — area of a rectangle
3. `area(double base, double height, boolean isTriangle)` — area of a triangle: `0.5 × base × height`

### Expected Output / Sample
```
Circle area (r=5):        78.54
Rectangle area (3x4):     12.00
Triangle area (6x4):      12.00
```

### Hints
- Use `Math.PI` for the circle.
- Use `String.format("%.2f", value)` for clean output.

### Bonus
Add a fourth overload for a square: `area(double side)` — but notice this conflicts with the circle! How do you resolve it? (Use a different parameter name and type, or use a boolean flag.)

---

## Exercise 7 — countVowels
**Difficulty:** Beginner
**Concepts:** method, String, loop, char comparison

### Problem
Write a method `countVowels(String s)` that returns the number of vowels (a, e, i, o, u — both upper and lower case) in the string.

### Expected Output / Sample
```
countVowels("Hello World")     → 3
countVowels("rhythm")          → 0
countVowels("AEIOU")           → 5
```

### Hints
- Use `.toLowerCase()` before checking.
- Check if the character is in the string `"aeiou"`.

### Bonus
Return a `Map<Character, Integer>` showing the count of each vowel.

---

## Exercise 8 — reverseWords
**Difficulty:** Intermediate
**Concepts:** method, String.split(), arrays, StringBuilder

### Problem
Write a method `reverseWords(String sentence)` that returns the sentence with the word order reversed. For example, `"Hello World Java"` → `"Java World Hello"`.

### Expected Output / Sample
```
reverseWords("Hello World Java")     → Java World Hello
reverseWords("one")                  → one
reverseWords("I love Java")          → Java love I
```

### Hints
- Use `sentence.split(" ")` to split into an array.
- Loop through the array in reverse.

### Bonus
Handle multiple spaces between words gracefully.

---

## Exercise 9 — celsius()
**Difficulty:** Beginner
**Concepts:** method, arithmetic, return type

### Problem
Write a method `celsius(double fahrenheit)` that converts a Fahrenheit temperature to Celsius using the formula: `(F - 32) × 5 / 9`. Also write `fahrenheit(double celsius)` for the reverse.

### Expected Output / Sample
```
celsius(212.0)     → 100.0
celsius(32.0)      → 0.0
fahrenheit(100.0)  → 212.0
fahrenheit(0.0)    → 32.0
```

### Hints
- Use `5.0 / 9` or `5.0 / 9.0` to avoid integer division.

### Bonus
Write a third method `describeTemperature(double celsius)` that returns "Freezing", "Cold", "Comfortable", "Warm", or "Hot" based on ranges you define.

---

## Exercise 10 — Recursive factorial()
**Difficulty:** Intermediate
**Concepts:** recursion, base case, long return type

### Problem
Write a recursive method `factorial(int n)` that returns `n!`. Recall: `0! = 1`, `1! = 1`, `n! = n × (n-1)!`. Use `long` as the return type to handle larger values.

### Expected Output / Sample
```
factorial(0)  → 1
factorial(5)  → 120
factorial(10) → 3628800
factorial(20) → 2432902008176640000
```

### Hints
- Base case: `n <= 1` → return 1.
- Recursive case: `return n * factorial(n - 1)`.

### Bonus
Add input validation — throw `IllegalArgumentException` for negative input.

---

## Exercise 11 — Recursive fibonacci()
**Difficulty:** Intermediate
**Concepts:** recursion, multiple recursive calls

### Problem
Write a recursive method `fibonacci(int n)` that returns the nth Fibonacci number. The sequence is: 0, 1, 1, 2, 3, 5, 8, 13, 21, ...

### Expected Output / Sample
```
fibonacci(0)  → 0
fibonacci(1)  → 1
fibonacci(6)  → 8
fibonacci(10) → 55
```

### Hints
- Base cases: `fibonacci(0) = 0`, `fibonacci(1) = 1`.
- Recursive case: `fibonacci(n-1) + fibonacci(n-2)`.

### Bonus
Time how long it takes to compute `fibonacci(40)` vs `fibonacci(45)`. Then write an iterative version and compare the speed.

---

## Exercise 12 — isPrime()
**Difficulty:** Intermediate
**Concepts:** method, loop, boolean, early return

### Problem
Write a method `isPrime(int n)` that returns `true` if `n` is a prime number. Use it to print all prime numbers from 2 to 50.

### Expected Output / Sample
```
Primes from 2 to 50:
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47
```

### Hints
- Numbers less than 2 are not prime.
- Loop from 2 to `Math.sqrt(n)` for efficiency.
- If any `i` divides `n` evenly, return `false`.

### Bonus
Write a method `nextPrime(int n)` that returns the first prime number greater than `n`.

---

## Exercise 13 — Recursive power()
**Difficulty:** Intermediate
**Concepts:** recursion, multiplication

### Problem
Write a recursive method `power(int base, int exponent)` that returns `base` raised to `exponent`. Do not use `Math.pow()`.

### Expected Output / Sample
```
power(2, 0)   → 1
power(2, 10)  → 1024
power(3, 4)   → 81
power(5, 3)   → 125
```

### Hints
- Base case: `exponent == 0` → return 1.
- Recursive case: `return base * power(base, exponent - 1)`.

### Bonus
Handle negative exponents: `power(2, -3)` = `1.0 / power(2, 3)` = `0.125`. Change the return type to `double`.

---

## Exercise 14 — countOccurrences()
**Difficulty:** Beginner
**Concepts:** method, String, loop, char

### Problem
Write a method `countOccurrences(String text, char target)` that returns the number of times `target` appears in `text`.

### Expected Output / Sample
```
countOccurrences("hello world", 'l')  → 3
countOccurrences("banana", 'a')       → 3
countOccurrences("java", 'z')         → 0
```

### Hints
- Loop over `text.toCharArray()` and count matches.

### Bonus
Make it case-insensitive: `countOccurrences("Hello", 'h')` = 1.

---

## Exercise 15 — String Calculator
**Difficulty:** Intermediate
**Concepts:** method, switch, exception handling

### Problem
Write a method `calculate(double a, String operator, double b)` that returns the result of the operation. Support `"+"`, `"-"`, `"*"`, `"/"`. For division by zero, throw an `ArithmeticException`. For unknown operators, throw an `IllegalArgumentException`.

### Expected Output / Sample
```
calculate(10, "+", 5)   → 15.0
calculate(10, "-", 3)   → 7.0
calculate(6, "*", 7)    → 42.0
calculate(10, "/", 4)   → 2.5
calculate(10, "/", 0)   → ArithmeticException: Division by zero
calculate(10, "%", 3)   → IllegalArgumentException: Unknown operator: %
```

### Hints
- Use a `switch` on the `operator` string.
- Throw exceptions with meaningful messages.

### Bonus
Add support for `"^"` (power) using your `power()` method from Exercise 13.
