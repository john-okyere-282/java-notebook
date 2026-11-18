# Project 01 — Command-Line Calculator

**Difficulty:** 🟢 Beginner  
**Estimated time:** 3–5 hours  
**Key skills:** Methods, control flow, Scanner, error handling, loops

> **Attempt the implementation yourself before reading the reference solution.**  
> Reference solution available in: `15-projects/solutions/README.md`

---

## Real-World Problem

You are building a utility tool for a small office. Staff need a simple calculator that runs in the terminal, allows multiple calculations in one session, and handles errors gracefully (no crashes from bad input or division by zero).

---

## Learning Objectives

By completing this project you will be able to:

- Organize logic into separate methods that do one thing each (Amalitech §4.2)
- Use `Scanner` to read user input from the terminal
- Use `switch` expressions (Java 14+) to route between operations
- Throw and catch custom exceptions
- Design a loop that runs until the user explicitly exits
- Call static methods from a utility class

---

## Requirements

### Core Features

1. **Addition** — add two numbers
2. **Subtraction** — subtract two numbers
3. **Multiplication** — multiply two numbers
4. **Division** — divide two numbers, with division-by-zero handled gracefully
5. **Multiple calculations** — after each result, ask if the user wants to calculate again
6. **Exit** — when the user enters `q` or `quit`, exit cleanly
7. **Invalid input** — if the user enters a non-number, show a helpful error and prompt again (do not crash)

### Non-functional Requirements

- Methods should be small (§4.1) — no method longer than 15 lines
- Calculator operations should be in a separate class from the user interface
- No silent exception catching (Amalitech §6.1)

---

## Suggested Folder Structure

```
calculator/
├── src/
│   ├── Calculator.java     ← arithmetic logic (no I/O here)
│   └── Main.java           ← user interface, loop, Scanner
└── (pom.xml optional)
```

---

## Class Responsibilities

### `Calculator`

This class contains only arithmetic. It does not know about the console, the user, or the loop.

| Method | Signature | Description |
|--------|-----------|-------------|
| `add` | `double add(double a, double b)` | Returns `a + b` |
| `subtract` | `double subtract(double a, double b)` | Returns `a - b` |
| `multiply` | `double multiply(double a, double b)` | Returns `a * b` |
| `divide` | `double divide(double a, double b)` | Returns `a / b`; throws `ArithmeticException` if `b == 0` |

### `Main`

This class handles all user interaction. It contains the `main` method and the program loop.

Responsibilities:
- Display the menu
- Read user input
- Delegate arithmetic to `Calculator`
- Display results
- Handle errors
- Loop until the user exits

---

## Implementation Steps

1. **Create the `Calculator` class** with the four methods. Test each one manually in `main` with hardcoded values before adding the interactive loop.

2. **Write the menu display.** Print the operations and their codes (e.g., `1. Add`, `2. Subtract`, `3. Multiply`, `4. Divide`, `q. Quit`).

3. **Read the operation choice.** Use `Scanner.nextLine()` to read the full line. Check if it is `"q"` or `"quit"` and exit if so.

4. **Handle invalid operation codes.** If the user enters `"5"` or `"abc"`, print an error and loop back to the menu.

5. **Read two numbers.** After a valid operation is chosen, prompt for the first number, then the second. Use a helper method to safely parse a `double` from a string — if parsing fails, print an error and loop back.

6. **Perform the calculation.** Call the appropriate `Calculator` method. Wrap the `divide` call in a `try-catch` to handle `ArithmeticException`.

7. **Display the result.** Format the output cleanly, e.g.: `Result: 10.0 + 5.0 = 15.0`

8. **Ask to continue.** After displaying the result, ask: `"Calculate again? (y/n)"`. If `n`, exit. If `y`, loop back to the menu.

9. **Close the Scanner** when exiting.

10. **Review your code** against Amalitech §3, §4, and §7 before you consider it done.

---

## Important Java Concepts Used

| Concept | Used for |
|---------|---------|
| `Scanner` | Reading user input from `System.in` |
| `Double.parseDouble()` | Converting String to double |
| `NumberFormatException` | Thrown when parsing invalid input |
| `ArithmeticException` | Thrown for division by zero |
| `switch` expression | Routing to the right operation |
| `while` loop | Running the calculator repeatedly |
| Static methods | Calculator operations (no state needed) |

---

## Expected Behavior — Sample Run

```
=== Command-Line Calculator ===
Operations: 1=Add  2=Subtract  3=Multiply  4=Divide  q=Quit

Enter operation: 1
Enter first number: 25
Enter second number: 17
Result: 25.0 + 17.0 = 42.0
Calculate again? (y/n): y

Enter operation: 4
Enter first number: 100
Enter second number: 0
Error: Cannot divide by zero.
Calculate again? (y/n): y

Enter operation: 2
Enter first number: abc
Error: "abc" is not a valid number. Please enter a number.
Calculate again? (y/n): y

Enter operation: q
Goodbye!
```

---

## Testing Requirements

Before submitting or marking this project complete, manually test these scenarios:

- [ ] All four operations with valid numbers
- [ ] Division by zero — application should not crash
- [ ] Non-numeric input (e.g., `"abc"`) — application should not crash
- [ ] Very large numbers (`1e15 + 1e15`)
- [ ] Negative numbers (`-5 * -3`)
- [ ] Decimal numbers (`3.14 * 2`)
- [ ] `q` exits cleanly at the operation prompt
- [ ] `q` exits cleanly at the "calculate again?" prompt

---

## Extension Challenges

After completing the core project, try these:

1. **Modulus operator** — Add `%` (remainder) as a fifth operation.
2. **Power operator** — Add `^` to raise a number to a power. Use `Math.pow()`.
3. **Square root** — Add `sqrt` as a single-operand operation.
4. **Calculation history** — Keep a list of past calculations in the session and display them when the user enters `h`.
5. **Unit tests** — Write JUnit 5 tests for the `Calculator` class. What do you need to test for `divide`?
6. **Input on one line** — Accept input in the format `10 + 5` and parse it into operand, operator, operand.

---

> ⚠️ **Do not look at the reference solution until you have a working implementation.**  
> The reference solution is in `15-projects/solutions/README.md`.
