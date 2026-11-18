# Exceptions — Introduction

## What is an exception?

An **exception** is an event that disrupts the normal flow of a program. When Java encounters a problem it cannot handle on its own — accessing a null reference, dividing by zero, trying to open a missing file — it creates an exception object that describes the problem and throws it up the call stack.

If nobody catches that exception, the program terminates and prints a stack trace.

---

## Errors vs Exceptions — The Critical Distinction

Both `Error` and `Exception` extend `Throwable`, but they are fundamentally different.

**Error** represents a serious problem in the JVM itself — `OutOfMemoryError`, `StackOverflowError`. These indicate that something is so broken (the JVM is out of memory, the stack is overflowed by infinite recursion) that you generally **cannot recover**. You should almost never catch an `Error`.

**Exception** represents conditions that your program can reasonably handle — a missing file, bad input, a failed network call. These are what you catch and handle.

```java
// This is what NOT to do
try {
    doSomething();
} catch (Throwable t) {
    // BAD — catches both Error and Exception
    // If Java is out of memory, you cannot "handle" that
}

// CORRECT — catch only what you can actually handle
try {
    doSomething();
} catch (IOException e) {
    // handle the specific problem
}
```

---

## Checked vs Unchecked Exceptions

**Checked exceptions** (extend `Exception` but not `RuntimeException`) must be either caught or declared in a `throws` clause. The compiler enforces this.

```java
// IOException is checked — compiler forces you to handle it
public void readFile(String path) throws IOException {  // declare it
    FileReader fr = new FileReader(path);  // might throw FileNotFoundException
    // ...
}

// Or handle it:
public void readFileSafely(String path) {
    try {
        FileReader fr = new FileReader(path);
    } catch (IOException e) {
        System.err.println("Could not read file: " + e.getMessage());
    }
}
```

**Unchecked exceptions** (extend `RuntimeException`) are not enforced by the compiler. They represent programming errors that ideally should not happen:

```java
String s = null;
s.length();           // NullPointerException at runtime — programming error

int[] arr = {1, 2, 3};
arr[5] = 10;          // ArrayIndexOutOfBoundsException — programming error

int x = Integer.parseInt("abc");  // NumberFormatException — invalid input
```

---

## Common Exceptions and Their Causes

| Exception | Cause |
|---|---|
| `NullPointerException` | Calling a method or accessing a field on a `null` reference |
| `ArrayIndexOutOfBoundsException` | Accessing an array index below 0 or ≥ length |
| `ClassCastException` | Downcasting to a type the object is not |
| `NumberFormatException` | Parsing a non-numeric string with `Integer.parseInt()` etc. |
| `IllegalArgumentException` | Passing an invalid argument to a method |
| `IllegalStateException` | Calling a method when the object is in an inappropriate state |
| `StackOverflowError` | Infinite recursion |
| `ArithmeticException` | Division by zero (integer division only) |
| `IOException` | File not found, read/write failure |
| `FileNotFoundException` | Specific file does not exist |
| `IndexOutOfBoundsException` | Parent of `ArrayIndexOutOfBoundsException` and `StringIndexOutOfBoundsException` |

---

## Reading a Stack Trace

When an exception is thrown and not caught, Java prints a stack trace. Here is how to read one:

```
Exception in thread "main"              ← which thread crashed
java.lang.NullPointerException:         ← exception type
  Cannot invoke "String.length()"       ← what went wrong (Java 17+ helpful messages)
  because "name" is null                ← why it went wrong
	at com.example.Main.printName(Main.java:12)   ← innermost frame (where it happened)
	at com.example.Main.processUser(Main.java:8)  ← the caller
	at com.example.Main.main(Main.java:4)         ← the entry point
```

**How to use this:**
1. Read the first line — it tells you the exception type.
2. Read the second line — it tells you what failed.
3. Look at the first frame — that is exactly where the error occurred (file and line number).
4. Work up the stack to understand how you got there.

**A common mistake:** ignoring everything except the first line. The stack trace tells a full story — read all of it.

---

## A Practical Demonstration

```java
public class StackTraceDemo {

    public static String toUpperCase(String s) {
        return s.toUpperCase();  // throws NullPointerException if s is null
    }

    public static void processName(String name) {
        String result = toUpperCase(name);  // name is null
        System.out.println(result);
    }

    public static void main(String[] args) {
        processName(null);
    }
}
```

This prints:
```
Exception in thread "main" java.lang.NullPointerException:
  Cannot invoke "String.toUpperCase()" because "s" is null
    at StackTraceDemo.toUpperCase(StackTraceDemo.java:3)
    at StackTraceDemo.processName(StackTraceDemo.java:7)
    at StackTraceDemo.main(StackTraceDemo.java:12)
```

The fix: check for null before calling `toUpperCase`, or validate inputs in `processName`.

---

## Throwing Exceptions

You can throw exceptions yourself using the `throw` keyword:

```java
public double divide(double a, double b) {
    if (b == 0) {
        throw new IllegalArgumentException("Divisor cannot be zero");
    }
    return a / b;
}
```

---

## Key Takeaways

- An exception interrupts normal program flow and travels up the call stack until caught.
- `Error` = JVM-level failure; never catch it.
- Checked exceptions: compiler forces you to handle or declare them.
- Unchecked (runtime) exceptions: usually programming errors; not compiler-enforced.
- Stack traces are detailed diagnostic tools — read every line.
- `throw new SomeException("message")` lets you signal problems explicitly.

---

## Related Topics

- [try-catch-finally](../try-catch-finally/README.md) — handling exceptions
- [Custom Exceptions](../custom-exceptions/README.md) — creating your own exception types
