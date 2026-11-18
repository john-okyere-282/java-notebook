# Strings

## What is a String?

A `String` is a sequence of characters. In Java, `String` is a class (not a primitive), but it is so fundamental that Java gives it special syntax: you can create one using a double-quoted literal without the `new` keyword.

```java
String name = "Alice";
String greeting = "Hello, World!";
String empty = "";
```

---

## String Immutability

Once a `String` object is created, its content cannot be changed. Every operation that appears to "modify" a String actually creates a new String object.

```java
String name = "John";
name = name + " Okyere";  // a NEW string is created; the original "John" still exists in memory
System.out.println(name); // John Okyere
```

**Why immutability matters:**
- Strings can safely be shared between threads
- Strings can be used as `HashMap` keys without risk of unintended mutation
- The JVM can optimise string storage through the string pool

---

## The String Pool

String literals are stored in a special area of heap memory called the **string pool**. When you write the same literal twice, the JVM reuses the same object rather than creating two.

```java
String a = "hello";
String b = "hello";
System.out.println(a == b);       // true  — same pool object (same reference)
System.out.println(a.equals(b));  // true  — same content

String c = new String("hello");
System.out.println(a == c);       // false — c is a new object outside the pool
System.out.println(a.equals(c));  // true  — same content
```

**Golden rule:** Always use `.equals()` to compare String values. Never use `==` unless you intentionally want to check if two references point to the same object (rare).

---

## Common String Methods

| Method | Description | Example | Output |
|--------|-------------|---------|--------|
| `length()` | Number of characters | `"Java".length()` | `4` |
| `charAt(int i)` | Character at index i | `"Java".charAt(1)` | `'a'` |
| `substring(int start)` | From start to end | `"Java21".substring(4)` | `"21"` |
| `substring(int start, int end)` | From start (inclusive) to end (exclusive) | `"Java21".substring(0, 4)` | `"Java"` |
| `contains(String s)` | True if s is found | `"Amalitech".contains("ali")` | `true` |
| `startsWith(String s)` | True if starts with s | `"Hello".startsWith("He")` | `true` |
| `endsWith(String s)` | True if ends with s | `"Hello".endsWith("lo")` | `true` |
| `indexOf(String s)` | First index of s, or -1 | `"Hello".indexOf("l")` | `2` |
| `lastIndexOf(String s)` | Last index of s | `"Hello".lastIndexOf("l")` | `3` |
| `replace(String old, String new)` | Replace all occurrences | `"aaaa".replace("a","b")` | `"bbbb"` |
| `toUpperCase()` | All uppercase | `"hello".toUpperCase()` | `"HELLO"` |
| `toLowerCase()` | All lowercase | `"HELLO".toLowerCase()` | `"hello"` |
| `trim()` | Remove leading/trailing whitespace | `" hi ".trim()` | `"hi"` |
| `strip()` | Like trim but Unicode-aware (Java 11+) | `" hi ".strip()` | `"hi"` |
| `isEmpty()` | True if length is 0 | `"".isEmpty()` | `true` |
| `isBlank()` | True if empty or only whitespace (Java 11+) | `"  ".isBlank()` | `true` |
| `split(String regex)` | Split into array | `"a,b,c".split(",")` | `["a","b","c"]` |
| `String.format(...)` | Formatted string | `String.format("%.2f", 3.14)` | `"3.14"` |
| `repeat(int n)` | Repeat n times (Java 11+) | `"ab".repeat(3)` | `"ababab"` |

```java
public class StringMethods {
    public static void main(String[] args) {
        String text = "  Amalitech Systems Engineering  ";

        System.out.println(text.strip());                       // Amalitech Systems Engineering
        System.out.println(text.strip().length());              // 33
        System.out.println(text.strip().toUpperCase());         // AMALITECH SYSTEMS ENGINEERING
        System.out.println(text.strip().contains("Systems"));   // true
        System.out.println(text.strip().replace("Systems", "Software"));
        // Amalitech Software Engineering

        String[] words = text.strip().split(" ");
        System.out.println("Word count: " + words.length);     // Word count: 3
        System.out.println("First word: " + words[0]);         // First word: Amalitech

        String formatted = String.format("Name: %-20s | Salary: $%,.2f", "John Okyere", 55000.0);
        System.out.println(formatted);
        // Name: John Okyere          | Salary: $55,000.00
    }
}
```

---

## String Concatenation

The `+` operator joins strings. When a non-String value is used with `+`, Java automatically converts it to a String.

```java
String name = "Alice";
int score = 95;
System.out.println("Player " + name + " scored " + score + " points.");
// Player Alice scored 95 points.
```

**Pitfall — `+` with numbers:**
```java
System.out.println("Value: " + 1 + 2);   // "Value: 12" — not "Value: 3"!
System.out.println("Value: " + (1 + 2)); // "Value: 3"  — parentheses force arithmetic first
```

---

## StringBuilder — for Performance

Strings are immutable. Concatenating with `+` inside a loop creates a new String object on every iteration, which is slow and wastes memory.

```java
// ❌ Slow — creates 10,000 String objects
String result = "";
for (int i = 0; i < 10_000; i++) {
    result += i;  // new String every time
}

// ✅ Fast — StringBuilder modifies a single buffer
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10_000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

**Key StringBuilder methods:**

| Method | What it does |
|--------|-------------|
| `append(value)` | Add to the end |
| `insert(int index, value)` | Insert at position |
| `delete(int start, int end)` | Remove characters |
| `reverse()` | Reverse the content |
| `toString()` | Convert to String |
| `length()` | Current length |

```java
StringBuilder sb = new StringBuilder("Java");
sb.append(" 21");        // "Java 21"
sb.insert(4, " SE");     // "Java SE 21"
sb.reverse();            // "12 ES avaJ"
System.out.println(sb);
```

---

## Text Blocks (Java 15+)

Text blocks let you write multi-line strings without escape characters, using `"""`:

```java
String json = """
        {
            "name": "John Okyere",
            "role": "Systems Engineer",
            "language": "Java"
        }
        """;
System.out.println(json);

String html = """
        <html>
            <body>
                <p>Hello, World!</p>
            </body>
        </html>
        """;
```

The indentation of the closing `"""` determines how much leading whitespace is stripped.

---

## String.format() and printf

For precise formatting:

```java
String name = "Alice";
int age = 28;
double salary = 65000.75;

// String.format returns a new String
String profile = String.format("%-15s | Age: %3d | Salary: $%,.2f", name, age, salary);
System.out.println(profile);
// Alice           | Age:  28 | Salary: $65,000.75

// printf prints directly (no return value)
System.out.printf("Pi to 4 decimal places: %.4f%n", Math.PI);
// Pi to 4 decimal places: 3.1416
```

**Common format specifiers:**

| Specifier | Meaning | Example |
|-----------|---------|---------|
| `%s` | String | `%s` → `"Alice"` |
| `%d` | Integer | `%5d` → right-aligned in width 5 |
| `%f` | Floating point | `%.2f` → 2 decimal places |
| `%n` | Newline (platform-independent) | — |
| `%b` | Boolean | `%b` → `true` |
| `%-10s` | Left-aligned string in width 10 | — |

---

## Exercises

1. Declare a String `sentence = "The quick brown fox jumps over the lazy dog"`. Print: its length, the character at index 10, everything from index 4 to 9, whether it contains "fox", the sentence in uppercase, the sentence with "fox" replaced by "cat".
2. Write a method that checks whether a string is a palindrome (reads the same forwards and backwards). Test with "racecar", "hello", and "madam".
3. Write a program that takes a full name (e.g. "John Emmanuel Okyere") and prints: the initials ("J. E. O."), the name in reverse word order ("Okyere Emmanuel John"), and the total character count excluding spaces.
4. Demonstrate the difference between `==` and `.equals()` using three String variables: one created as a literal, one as `new String(...)` with the same value, and one as a literal with the same value. Print the results of `==` and `.equals()` for each pair.
5. Use `StringBuilder` to build a string that contains the numbers 1 through 20, each separated by a comma (no trailing comma). Print the final string.

---

## Key Takeaways

- `String` is a class, not a primitive — treat it accordingly
- Strings are immutable — operations create new Strings
- Always use `.equals()` to compare string content, never `==`
- The string pool saves memory for identical literals
- Use `StringBuilder` when building strings in a loop
- `String.format()` / `printf` give precise control over output formatting

---

## Related Topics

- [Data Types](../data-types/README.md) — primitives vs reference types
- [User Input](../user-input/README.md) — reading strings from the keyboard
- [Modern Java — Text Blocks](../../07-modern-java/var-records-textblocks/README.md)
