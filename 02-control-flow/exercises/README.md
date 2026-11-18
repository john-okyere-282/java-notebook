# Phase 02 — Control Flow Exercises

Practice exercises covering if statements, switch, loops, break, and continue.

---

## Exercise 1 — Number Classifier
**Difficulty:** Beginner
**Concepts:** if-else, Scanner

### Problem
Write a program that asks the user to enter a number and prints whether it is `"positive"`, `"negative"`, or `"zero"`.

### Expected Output / Sample
```
Enter a number: -7
Negative
```
```
Enter a number: 0
Zero
```

### Hints
- Use `Scanner` to read user input.
- Three branches: `> 0`, `< 0`, and the `else` (which must be zero).

### Bonus
Extend it to also print "even" or "odd" for non-zero numbers.

---

## Exercise 2 — Grade Calculator
**Difficulty:** Beginner
**Concepts:** else-if chain

### Problem
Write a program that takes a numeric score (0–100) and prints the corresponding letter grade:
- 90–100 → A
- 80–89 → B
- 70–79 → C
- 60–69 → D
- Below 60 → F

### Expected Output / Sample
```
Enter score: 83
Grade: B
```

### Hints
- Use an `else-if` chain.
- Handle invalid scores (below 0 or above 100) with an error message.

### Bonus
Print the grade description alongside the letter: `"B — Good"`.

---

## Exercise 3 — FizzBuzz
**Difficulty:** Beginner
**Concepts:** for loop, if-else, modulus operator

### Problem
Print the numbers from 1 to 100. For multiples of 3 print `"Fizz"`, for multiples of 5 print `"Buzz"`, and for multiples of both 3 and 5 print `"FizzBuzz"`.

### Expected Output / Sample
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
...
```

### Hints
- Check the combined condition (`% 3 == 0 && % 5 == 0`) first, otherwise it will never be reached.

### Bonus
Make it work for any range entered by the user.

---

## Exercise 4 — Even Numbers
**Difficulty:** Beginner
**Concepts:** for loop, modulus

### Problem
Print all even numbers from 1 to 50 using a `for` loop.

### Expected Output / Sample
```
2 4 6 8 10 12 14 16 18 20 22 24 26 28 30 32 34 36 38 40 42 44 46 48 50
```

### Hints
- Either step by 2 (`i += 2`) or use `% 2 == 0` inside the loop.

### Bonus
Print the sum of all even numbers from 1 to 50.

---

## Exercise 5 — Sum of Digits
**Difficulty:** Beginner
**Concepts:** while loop, modulus, integer division

### Problem
Write a program that takes a positive integer from the user and calculates the sum of its digits. For example, 123 → 1 + 2 + 3 = 6.

### Expected Output / Sample
```
Enter a number: 456
Sum of digits: 15
```

### Hints
- Use `% 10` to get the last digit.
- Use `/ 10` to remove the last digit.
- Loop while the number is greater than 0.

### Bonus
Count how many digits the number has as well.

---

## Exercise 6 — Multiplication Table
**Difficulty:** Beginner
**Concepts:** for loop, Scanner, formatted output

### Problem
Ask the user to enter a number. Print its multiplication table from 1 to 12.

### Expected Output / Sample
```
Enter a number: 7
7 x  1 =  7
7 x  2 = 14
7 x  3 = 21
...
7 x 12 = 84
```

### Hints
- Use `printf` or `String.format` to align the output neatly.

### Bonus
Let the user choose the range (e.g. 1 to N instead of 1 to 12).

---

## Exercise 7 — Reverse a Number
**Difficulty:** Intermediate
**Concepts:** while loop, modulus, integer division

### Problem
Write a program that reverses a positive integer entered by the user. For example, 1234 → 4321.

### Expected Output / Sample
```
Enter a number: 1234
Reversed: 4321
```
```
Enter a number: 100
Reversed: 1
```

### Hints
- Use `% 10` to extract the last digit, then build the reversed number by multiplying by 10.
- `reversed = reversed * 10 + lastDigit`

### Bonus
Print whether the original number is a palindrome (reads the same forwards and backwards).

---

## Exercise 8 — Count Vowels
**Difficulty:** Intermediate
**Concepts:** for-each over char array, String.toCharArray(), switch or if

### Problem
Write a program that takes a word or sentence from the user and counts the number of vowels (a, e, i, o, u — both upper and lower case).

### Expected Output / Sample
```
Enter a string: Hello World
Vowels: 3
```

### Hints
- Use `s.toLowerCase().toCharArray()` and loop over each character.
- Check if the character is in `"aeiou"`.

### Bonus
Also count consonants and print both totals.

---

## Exercise 9 — Number Pattern (Nested Loops)
**Difficulty:** Intermediate
**Concepts:** nested for loops

### Problem
Print the following triangle pattern using nested loops:
```
1
1 2
1 2 3
1 2 3 4
1 2 3 4 5
```

### Expected Output / Sample
Exactly as shown above.

### Hints
- Outer loop controls the row number (1 to 5).
- Inner loop prints numbers from 1 to the current row number.

### Bonus
Print the pattern upside down (5 rows down to 1 row).

---

## Exercise 10 — Largest of Three Numbers
**Difficulty:** Beginner
**Concepts:** Scanner, if-else

### Problem
Ask the user to enter three numbers. Print the largest one.

### Expected Output / Sample
```
Enter three numbers: 42 17 89
Largest: 89
```

### Hints
- Compare the first two, then compare the winner against the third.
- Or use nested if-else.

### Bonus
Print all three numbers sorted from smallest to largest without using `Arrays.sort()`.

---

## Exercise 11 — Simple Menu Loop
**Difficulty:** Intermediate
**Concepts:** do-while loop, switch, Scanner

### Problem
Write a program with a menu that keeps showing until the user chooses to quit:
```
=== Menu ===
1. Say Hello
2. Print current count
3. Quit
Choose option:
```
Options 1 and 2 perform their action and show the menu again. Option 3 exits.

### Expected Output / Sample
```
=== Menu ===
1. Say Hello
2. Print current count
3. Quit
Choose option: 1
Hello!

=== Menu ===
...
Choose option: 3
Goodbye!
```

### Hints
- Use `do-while` so the menu always shows at least once.
- Use `switch` to handle the option.
- Keep a `count` variable that increments each time option 1 is chosen.

### Bonus
Add option 4 to reset the count to zero.

---

## Exercise 12 — Number Guessing Game (Single Guess)
**Difficulty:** Intermediate
**Concepts:** if-else, comparison, Scanner

### Problem
Pick a secret number (hardcode it as 42, or use `Math.random()`). Ask the user to guess it. Print "Too high", "Too low", or "Correct!" based on their guess.

### Expected Output / Sample
```
Guess the number (1-100): 60
Too high!

Guess the number (1-100): 30
Too low!

Guess the number (1-100): 42
Correct! Well done.
```

### Hints
- For a single-guess version, just one `if-else-if` is sufficient.

### Bonus
Wrap it in a loop so the user keeps guessing until they get it right. Count and print the number of attempts.

---

## Exercise 13 — Sum from 1 to N
**Difficulty:** Beginner
**Concepts:** for loop, Scanner

### Problem
Ask the user to enter a positive integer N. Print the sum of all numbers from 1 to N.

### Expected Output / Sample
```
Enter N: 10
Sum from 1 to 10 = 55
```

### Hints
- Use a `for` loop from 1 to N (inclusive).
- Alternatively, use the formula `N * (N + 1) / 2` and compare results.

### Bonus
Also print the sum of only the odd numbers and the sum of only the even numbers from 1 to N.

---

## Exercise 14 — Print All Factors
**Difficulty:** Intermediate
**Concepts:** for loop, modulus

### Problem
Ask the user to enter a positive integer. Print all of its factors (numbers that divide it evenly).

### Expected Output / Sample
```
Enter a number: 24
Factors of 24: 1 2 3 4 6 8 12 24
```

### Hints
- Loop from 1 to the number.
- If `number % i == 0`, then `i` is a factor.

### Bonus
Count and print the total number of factors. Use this to check: a prime number has exactly 2 factors (1 and itself).

---

## Exercise 15 — Prime Number Checker
**Difficulty:** Intermediate
**Concepts:** for loop, break, boolean flag

### Problem
Ask the user to enter a positive integer greater than 1. Determine whether it is a prime number.

A prime number is divisible only by 1 and itself.

### Expected Output / Sample
```
Enter a number: 17
17 is prime.
```
```
Enter a number: 15
15 is not prime. (Divisible by 3 and 5)
```

### Hints
- Loop from 2 to `number - 1`. If any `i` divides the number evenly, it is not prime — `break`.
- More efficient: loop from 2 to `Math.sqrt(number)`.

### Bonus
Print all prime numbers from 1 to 100 (the Sieve approach is a stretch goal).
