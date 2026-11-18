# try-catch-finally

## Basic `try-catch` Syntax

```java
try {
    // Code that might throw an exception
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // Handle the exception
    System.err.println("Cannot divide by zero: " + e.getMessage());
}
```

The `try` block wraps code that might fail. If an exception is thrown, Java immediately stops executing the `try` block and jumps to the matching `catch` block. Code after the throw point in the `try` block does not run.

---

## Multiple `catch` Blocks

You can catch different exception types with separate blocks:

```java
public static int parseAndDivide(String numStr, int divisor) {
    try {
        int number = Integer.parseInt(numStr);   // might throw NumberFormatException
        return number / divisor;                 // might throw ArithmeticException
    } catch (NumberFormatException e) {
        System.err.println("Invalid number format: " + numStr);
        return 0;
    } catch (ArithmeticException e) {
        System.err.println("Division by zero");
        return 0;
    }
}
```

**Order matters.** Always catch more specific exceptions before more general ones. If you catch `Exception` before `IOException`, the `IOException` branch is unreachable:

```java
// WRONG — Exception catches everything; IOException is unreachable
try {
    readFile();
} catch (Exception e) {       // catches everything
    ...
} catch (IOException e) {     // COMPILE ERROR: unreachable
    ...
}

// CORRECT — specific first, general last
try {
    readFile();
} catch (FileNotFoundException e) {   // most specific
    ...
} catch (IOException e) {             // parent of FileNotFoundException
    ...
} catch (Exception e) {               // most general — only if truly needed
    ...
}
```

---

## Multi-Catch (Java 7+)

When you want to handle multiple exception types the same way, combine them with `|`:

```java
try {
    String data = Files.readString(Path.of("config.json"));
    int value = Integer.parseInt(data.trim());
} catch (IOException | NumberFormatException e) {
    System.err.println("Failed to load config: " + e.getMessage());
}
```

The combined type must not be in a parent-child relationship — if `FileNotFoundException` is a subtype of `IOException`, you cannot write `catch (IOException | FileNotFoundException e)`.

---

## The `finally` Block

Code in `finally` **always** runs — whether the `try` block succeeded, threw a caught exception, or threw an uncaught exception. Use it to release resources.

```java
Connection conn = null;
try {
    conn = getConnection();
    // ... use connection
} catch (SQLException e) {
    System.err.println("Query failed: " + e.getMessage());
} finally {
    if (conn != null) {
        try {
            conn.close();  // always close the connection
        } catch (SQLException e) {
            System.err.println("Failed to close connection: " + e.getMessage());
        }
    }
}
```

`finally` runs even if the `catch` block itself throws an exception, and even if there is a `return` statement in the `try` block.

---

## try-with-resources (Java 7+)

Managing resources in `finally` is verbose and error-prone. `try-with-resources` automatically closes any `AutoCloseable` resource when the block exits:

```java
// Without try-with-resources — verbose and risky
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("data.txt"));
    String line = br.readLine();
    System.out.println(line);
} catch (IOException e) {
    System.err.println("Read failed: " + e.getMessage());
} finally {
    if (br != null) {
        try { br.close(); } catch (IOException e) { /* ignored */ }
    }
}

// With try-with-resources — clean and safe
try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
    String line = br.readLine();
    System.out.println(line);
} catch (IOException e) {
    System.err.println("Read failed: " + e.getMessage());
}
// br is automatically closed — even if an exception is thrown
```

You can open multiple resources in one statement:

```java
try (InputStream input = new FileInputStream("in.txt");
     OutputStream output = new FileOutputStream("out.txt")) {
    // both are closed automatically in reverse order
}
```

Any class that implements `AutoCloseable` (or `Closeable`) works with try-with-resources.

---

## Catching vs Rethrowing

Sometimes you can handle the exception at the current level. Sometimes you should log and rethrow, or wrap it in a more meaningful exception:

```java
// Handle it completely
try {
    int value = Integer.parseInt(input);
} catch (NumberFormatException e) {
    return 0;  // graceful default
}

// Log and rethrow the same exception
try {
    database.save(record);
} catch (SQLException e) {
    logger.error("Failed to save record " + record.getId(), e);
    throw e;  // rethrow — let the caller decide what to do
}

// Wrap in a more meaningful exception
try {
    database.save(record);
} catch (SQLException e) {
    throw new DataPersistenceException("Could not save record: " + record.getId(), e);
    //                                                                            ↑ preserve the cause
}
```

---

## Full Practical Example

```java
import java.io.*;
import java.nio.file.*;

public class FileProcessor {

    public static String readFirstLine(String filePath) {
        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            String line = reader.readLine();
            if (line == null) {
                throw new IllegalStateException("File is empty: " + filePath);
            }
            return line;
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + filePath);
            return null;
        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
            return null;
        }
    }

    public static int parsePositiveInt(String text) {
        try {
            int value = Integer.parseInt(text.trim());
            if (value <= 0) {
                throw new IllegalArgumentException("Value must be positive, got: " + value);
            }
            return value;
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Cannot parse '" + text + "' as an integer", e);
        }
    }

    public static void main(String[] args) {
        // Test readFirstLine
        String line = readFirstLine("nonexistent.txt");
        System.out.println("Line: " + line);  // null — handled gracefully

        // Test parsePositiveInt
        try {
            System.out.println(parsePositiveInt("42"));    // 42
            System.out.println(parsePositiveInt("abc"));   // throws
        } catch (IllegalArgumentException e) {
            System.err.println("Parse error: " + e.getMessage());
        }
    }
}
```

---

## Common Mistakes

**1. Catching `Exception` or `Throwable` unnecessarily**
```java
// BAD — hides bugs; catches things you have no idea how to handle
try {
    doSomething();
} catch (Exception e) {
    // silently swallowed
}
```

**2. Empty catch blocks (swallowing exceptions)**
```java
try {
    readFile();
} catch (IOException e) {
    // nothing — the error disappears and nobody knows
}
```

**3. Using `finally` for logic that depends on success**
```java
try {
    result = compute();
} finally {
    sendEmail(result);  // runs even if compute() threw — result may be null/wrong
}
```

**4. Catching a checked exception that is not thrown**
```java
try {
    int x = 5;
} catch (IOException e) {  // COMPILE ERROR — IOException is never thrown here
    ...
}
```

---

## Key Takeaways

- `try` wraps risky code. `catch` handles specific exception types. `finally` always runs.
- Catch more specific exceptions before more general ones.
- Use multi-catch for identical handling of multiple types.
- Use `try-with-resources` for anything that implements `AutoCloseable` — it is cleaner and safer than `finally`.
- Never swallow exceptions silently. At minimum, log them.

---

## Related Topics

- [Custom Exceptions](../custom-exceptions/README.md) — creating your own exception types
- [Best Practices](../best-practices/README.md) — what never to do with exceptions
