# Phase 01 — Exercises

Complete these exercises to reinforce everything from Phase 01. Create one `.java` file per exercise in this folder. Try each exercise yourself before looking at any hints.

**Difficulty scale:** ⭐ Beginner | ⭐⭐ Intermediate | ⭐⭐⭐ Challenge

---

## Exercise 1 — Personal Profile ⭐

**Concepts:** Variables, String, printing  
**File:** `Exercise01PersonalProfile.java`

Declare variables for your full name, age, job title, and years of experience. Print a formatted profile card exactly matching the output below.

**Expected Output:**
```
=== Employee Profile ===
Name:        John Okyere
Age:         25
Role:        Systems Engineer
Experience:  2 years
```

**Hint:** Use `System.out.printf` with a format string like `"%-12s %s%n"` to align the labels.

---

## Exercise 2 — Temperature Converter ⭐

**Concepts:** Variables, arithmetic operators, double, printf  
**File:** `Exercise02TemperatureConverter.java`

Given a temperature in Celsius (e.g. 37.0), calculate and print the same temperature in Fahrenheit and Kelvin.

**Formulas:**
- Fahrenheit: `F = (C × 9.0 / 5) + 32`
- Kelvin: `K = C + 273.15`

**Expected Output:**
```
37.0°C = 98.6°F
37.0°C = 310.15K
```

**Hint:** Use `double` for all variables. Use `printf` with `%.1f` and `%.2f`.

---

## Exercise 3 — Shopping Bill ⭐

**Concepts:** Arithmetic, printf  
**File:** `Exercise03ShoppingBill.java`

You buy three items: a pen (GHS 8.50), a notebook (GHS 25.00), and a calculator (GHS 65.00). Apply a 10% discount to the total. Print an itemised bill.

**Expected Output:**
```
=== Shopping Bill ===
Pen             GHS   8.50
Notebook        GHS  25.00
Calculator      GHS  65.00
----------------------------
Subtotal        GHS  98.50
Discount (10%)  GHS  -9.85
----------------------------
Total           GHS  88.65
```

**Hint:** Calculate `discount = subtotal * 0.10`. Use `printf` with `%-16s GHS %6.2f%n`.

---

## Exercise 4 — String Inspector ⭐

**Concepts:** String methods  
**File:** `Exercise04StringInspector.java`

Given the String `"Amalitech Systems Engineering"`, print the following (each on its own line with a label):
1. The total number of characters
2. The string in uppercase
3. The first word only (use `indexOf` and `substring`)
4. Whether the string contains `"Systems"` (true/false)
5. The string with `"Systems"` replaced by `"Software"`

---

## Exercise 5 — Type Casting Practice ⭐

**Concepts:** Type casting, primitive types  
**File:** `Exercise05TypeCasting.java`

Given:
```java
double price = 19.99;
int quantity = 7;
long largeNumber = 9_999_999_999L;
```

Perform and print:
1. The total cost as a `double`
2. The total cost cast to an `int` (truncated — explain in a comment why the decimal is dropped, not rounded)
3. `largeNumber` divided by `1_000_000` as a `double`
4. The maximum value of `int` using `Integer.MAX_VALUE`

---

## Exercise 6 — Operator Practice ⭐

**Concepts:** All operators  
**File:** `Exercise06Operators.java`

Declare `int a = 15` and `int b = 4`.

Print the result of every arithmetic operator. Then:
- Demonstrate pre-increment on a copy of `a`
- Demonstrate post-increment on a copy of `a`
- Use the ternary operator to print whether `a` is greater than `b`
- Use `a += 10` and print the new value

**Expected Output (partial):**
```
a + b = 19
a - b = 11
a * b = 60
a / b = 3
a % b = 3
Pre-increment of a: 16
Post-increment (value used): 15, (then a becomes 16)
a > b: true
a after += 10: 25
```

---

## Exercise 7 — User Input Greeting ⭐

**Concepts:** Scanner, String, int  
**File:** `Exercise07UserGreeting.java`

Ask the user for their name, age, and favourite programming language. Print a personalised greeting.

**Sample Input:**
```
Alice
28
Python
```

**Sample Output:**
```
Welcome, Alice! At 28, you're learning Python. Great choice!
```

---

## Exercise 8 — Even or Odd ⭐

**Concepts:** Scanner, modulo, if-else  
**File:** `Exercise08EvenOdd.java`

Ask the user to enter a whole number. Print whether it is even or odd.

**Sample Output:**
```
Enter a number: 7
7 is odd.
```

**Hint:** A number is even if `number % 2 == 0`.

---

## Exercise 9 — BMI Calculator ⭐⭐

**Concepts:** Scanner, arithmetic, double, if-else  
**File:** `Exercise09BMI.java`

Ask the user for their weight in kilograms and height in metres. Calculate BMI using the formula: `BMI = weight / (height * height)`. Print the BMI to 2 decimal places and a category.

**BMI Categories:**
- Underweight: BMI < 18.5
- Normal weight: 18.5 ≤ BMI < 25.0
- Overweight: 25.0 ≤ BMI < 30.0
- Obese: BMI ≥ 30.0

**Sample Output:**
```
Enter weight (kg): 70
Enter height (m): 1.75
BMI: 22.86 — Normal weight
```

---

## Exercise 10 — String Methods Challenge ⭐⭐

**Concepts:** String methods, Scanner  
**File:** `Exercise10StringChallenge.java`

Ask the user to enter a sentence (at least 3 words). Print:
1. Total character count (including spaces)
2. Character count excluding spaces
3. The number of words (count spaces + 1)
4. The sentence in reverse (use `StringBuilder.reverse()`)
5. Whether the sentence starts and ends with the same character (case-insensitive)

---

## Exercise 11 — Swap Without Temp Variable ⭐⭐

**Concepts:** Arithmetic  
**File:** `Exercise11Swap.java`

Swap two integers (`a = 100`, `b = 200`) without using a third variable. Use arithmetic:
```
a = a + b
b = a - b
a = a - b
```

Print the values before and after. Add a comment explaining *why* this works mathematically.

---

## Exercise 12 — Circle Calculator ⭐⭐

**Concepts:** Math.PI, double, printf  
**File:** `Exercise12Circle.java`

Ask the user to enter a radius. Calculate and print the circumference (`2πr`) and area (`πr²`) to 4 decimal places. Use `Math.PI` for π.

**Sample Output:**
```
Enter radius: 5.0
Circumference: 31.4159
Area:          78.5398
```

---

## Exercise 13 — Type Limits Explorer ⭐⭐

**Concepts:** Primitive types, overflow, wrapper constants  
**File:** `Exercise13TypeLimits.java`

Print the maximum and minimum values of `int`, `long`, and `double` using their wrapper class constants (`Integer.MAX_VALUE`, etc.). Then demonstrate integer overflow: assign `Integer.MAX_VALUE` to an `int`, add 1, and print the result. Explain what happened in a comment.

Also print:
- `Integer.SIZE` (number of bits)
- `Long.SIZE`
- `Double.SIZE`

---

## Exercise 14 — Palindrome Number Check ⭐⭐

**Concepts:** String, charAt, loop  
**File:** `Exercise14Palindrome.java`

Ask the user to enter a number. Convert it to a String. Check whether it reads the same forwards and backwards.

**Sample Output:**
```
Enter a number: 121
121 is a palindrome.

Enter a number: 123
123 is not a palindrome.
```

**Hint:** Compare `str.charAt(i)` with `str.charAt(str.length() - 1 - i)` for all positions up to the midpoint.

---

## Exercise 15 — Full Name Formatter ⭐⭐

**Concepts:** Scanner, String methods  
**File:** `Exercise15NameFormatter.java`

Ask the user to enter their first name, middle name, and last name separately. Print:
1. Full name (all three joined with spaces)
2. Initials (e.g. `"J. E. O."`)
3. Reversed format: `"LastName, FirstName MiddleName"`
4. Total character count of the full name, excluding spaces

**Sample Output:**
```
Enter first name: John
Enter middle name: Emmanuel
Enter last name: Okyere

Full name:   John Emmanuel Okyere
Initials:    J. E. O.
Reversed:    Okyere, John Emmanuel
Chars (no spaces): 18
```

---

## Checklist

Before moving to Phase 02, confirm:

- [ ] Exercise 01 — Personal Profile
- [ ] Exercise 02 — Temperature Converter
- [ ] Exercise 03 — Shopping Bill
- [ ] Exercise 04 — String Inspector
- [ ] Exercise 05 — Type Casting Practice
- [ ] Exercise 06 — Operator Practice
- [ ] Exercise 07 — User Input Greeting
- [ ] Exercise 08 — Even or Odd
- [ ] Exercise 09 — BMI Calculator
- [ ] Exercise 10 — String Methods Challenge
- [ ] Exercise 11 — Swap Without Temp Variable
- [ ] Exercise 12 — Circle Calculator
- [ ] Exercise 13 — Type Limits Explorer
- [ ] Exercise 14 — Palindrome Number Check
- [ ] Exercise 15 — Full Name Formatter

---

## Standards to Apply

Every Java file you create should follow:
- Amalitech §3.1: meaningful variable names
- Amalitech §3.5: lowerCamelCase; no Hungarian notation
- Amalitech §10.1: comment the *why*, not the *what*
- Amalitech §11.1: one declaration per line
