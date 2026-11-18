# Debugging in Java

## What is Debugging?

Debugging is the process of finding and fixing errors (bugs) in your code. Every programmer debugs — the skill is learning to do it efficiently rather than staring at code hoping the answer appears.

---

## Types of Errors

### 1. Compile-time errors (Syntax errors)

The Java compiler rejects the code before it can run. These are the easiest errors to fix because the compiler tells you exactly what is wrong and where.

```java
public class SyntaxError {
    public static void main(String[] args) {
        int age = 25
        System.out.println(age);  // ← compiler errors here too
    }
}
```

**Compiler output:**
```
SyntaxError.java:3: error: ';' expected
        int age = 25
                    ^
```

Common causes: missing `;`, mismatched `{}` brackets, misspelled keyword, wrong capitalisation (`String` not `string`).

### 2. Runtime errors (Exceptions)

The code compiles but crashes during execution. These are reported as exceptions with a **stack trace**.

```java
public class RuntimeError {
    public static void main(String[] args) {
        String name = null;
        System.out.println(name.length());  // crashes here
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.NullPointerException:
  Cannot invoke "String.length()" because "name" is null
    at RuntimeError.main(RuntimeError.java:4)
```

### 3. Logic errors

The code compiles and runs without crashing, but the output is wrong. These are the hardest to find.

```java
// Intended: calculate average of 3 numbers
int a = 10, b = 20, c = 30;
double average = a + b + c / 3;         // ❌ wrong: 10 + 20 + (30/3) = 40
double correct = (double)(a + b + c) / 3; // ✅ correct: 20.0
System.out.println(average);  // 40.0 — no error, but wrong!
```

---

## Reading a Stack Trace

A stack trace is the most useful tool for diagnosing a runtime error. Learn to read it from the top.

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 5 out of bounds for length 3
	at com.amalitech.demo.OrderProcessor.getItem(OrderProcessor.java:42)
	at com.amalitech.demo.OrderProcessor.processAll(OrderProcessor.java:28)
	at com.amalitech.demo.Main.main(Main.java:10)
```

| Part | What it tells you |
|------|------------------|
| `Exception in thread "main"` | The error happened in the main thread |
| `java.lang.ArrayIndexOutOfBoundsException` | The type of exception |
| `Index 5 out of bounds for length 3` | The specific detail — tried to access index 5 in an array of length 3 |
| `at ...OrderProcessor.java:42` | The exact class and line where the error occurred — start here |
| Lines below | The call chain — how the program got to line 42 |

**Strategy:** Start reading at the first `at` line that belongs to *your* code (not Java's standard library). That is where to look first.

---

## Common Beginner Exceptions

| Exception | Cause | Fix |
|-----------|-------|-----|
| `NullPointerException` | Calling a method or accessing a field on a `null` reference | Check for null before use: `if (obj != null)` |
| `ArrayIndexOutOfBoundsException` | Accessing array index < 0 or >= array length | Check bounds: `if (i >= 0 && i < arr.length)` |
| `StringIndexOutOfBoundsException` | `charAt(i)` where i < 0 or >= string length | Check: `if (i < str.length())` |
| `ClassCastException` | Casting to an incompatible type | Use `instanceof` before casting |
| `NumberFormatException` | `Integer.parseInt("abc")` — string is not a valid number | Validate the string first |
| `StackOverflowError` | Infinite recursion — no base case reached | Ensure recursion has a reachable base case |
| `ArithmeticException: / by zero` | Integer division by zero | Check divisor: `if (divisor != 0)` |

---

## Print Debugging

The simplest debugging technique: add `System.out.println()` statements to print variable values at key points.

```java
public int calculateTotal(int price, int quantity, int discountPercent) {
    System.out.println("DEBUG: price=" + price + " qty=" + quantity + " discount=" + discountPercent);

    int subtotal = price * quantity;
    System.out.println("DEBUG: subtotal=" + subtotal);

    int discount = subtotal * discountPercent / 100;
    System.out.println("DEBUG: discount=" + discount);

    int total = subtotal - discount;
    System.out.println("DEBUG: total=" + total);

    return total;
}
```

**Rules for print debugging:**
- Remove all debug prints before committing your code
- Label prints clearly: prefix with `DEBUG:` so you can find and remove them later
- Print both the variable name and its value: `"DEBUG: count=" + count`

---

## IntelliJ IDEA Debugger

The debugger lets you pause your program at any line, inspect every variable, and step through code one statement at a time. It is far more powerful than print statements for complex bugs.

### Setting a Breakpoint

Click in the grey gutter to the left of a line number. A red dot appears. When you run in debug mode, execution will pause at that line.

### Running in Debug Mode

Click the bug icon (🐛) next to the run button, or press `Shift+F9`.

### Debugger Controls

| Button / Key | Action |
|-------------|--------|
| `F8` | Step Over — execute the current line and move to the next |
| `F7` | Step Into — enter the method being called |
| `Shift+F8` | Step Out — finish the current method and return to the caller |
| `F9` | Resume — continue running until the next breakpoint |
| `Alt+F8` | Evaluate expression — type any expression and see its value |

### Variables Panel

When paused, the **Variables** panel (bottom-left) shows every variable in scope with its current value. You can expand objects to see their fields.

### Watches

In the Watches panel, add expressions to monitor. As you step through code, IntelliJ evaluates them and shows you the current value at each step.

---

## Debugging Workflow — Step by Step

1. **Identify the symptom**: what output are you getting? What did you expect?
2. **Find the error type**: compile error? runtime exception? wrong output?
3. **Read the message**: what does the exception say? What line?
4. **Form a hypothesis**: what could cause this?
5. **Test the hypothesis**: add a breakpoint or print statement near the suspected line
6. **Verify or revise**: does the evidence confirm your guess? If not, narrow down further
7. **Fix and test**: make the change, then run your tests to confirm it is fixed and nothing else broke

---

## Practical Exercise — Debug This

The following code has three bugs (one compile, one runtime, one logic). Find and fix all three:

```java
public class BuggyCalculator {
    public static void main(String[] args) {
        int x = 10;
        int y = 0;

        // Bug 1: will this compile?
        System.out.println("Sum: " + x + y)

        // Bug 2: what happens at runtime?
        int result = x / y;
        System.out.println("Division: " + result);

        // Bug 3: is the average correct?
        double average = x + y / 2;
        System.out.println("Average: " + average);
    }
}
```

---

## Key Takeaways

- Three error types: compile-time (syntax), runtime (exceptions), logic (wrong output)
- Stack traces tell you the exception type, the message, and the exact line — read from the top
- Print debugging is quick; the IntelliJ debugger is powerful for complex issues
- Remove all debug prints before committing
- The debugging workflow: identify → hypothesise → test → fix → verify

---

## Related Topics

- [Comments](../comments/README.md) — writing TODO and FIXME comments during debugging
- [Exception Handling](../../06-exception-handling/README.md) — handling runtime errors properly
- [Testing](../../10-testing/README.md) — preventing bugs with unit tests
