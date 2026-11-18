# Data Types

## What are Data Types?

A data type tells Java three things about a variable:
1. What kind of value it can hold
2. How much memory to reserve
3. What operations are valid on that value

Declaring the wrong type for a variable causes a compile error. This is one of Java's strengths: the compiler catches mismatches before the program runs.

**Real-world analogy:** Different types of storage containers suit different contents. A small coin pouch (byte), a wallet (int), a briefcase (long), a measuring jug for decimals (double), a light switch for yes/no (boolean). Trying to store a car in a coin pouch fails — Java's type system prevents this kind of mismatch.

---

## The 8 Primitive Types

| Type | Size | Minimum | Maximum | Default | Literal example |
|------|------|---------|---------|---------|----------------|
| `byte` | 8 bits | -128 | 127 | 0 | `byte b = 100;` |
| `short` | 16 bits | -32,768 | 32,767 | 0 | `short s = 1000;` |
| `int` | 32 bits | -2,147,483,648 | 2,147,483,647 | 0 | `int i = 42;` |
| `long` | 64 bits | -9,223,372,036,854,775,808 | 9,223,372,036,854,775,807 | 0L | `long l = 10_000_000_000L;` |
| `float` | 32 bits | ~1.4E-45 | ~3.4E+38 | 0.0f | `float f = 3.14f;` |
| `double` | 64 bits | ~5E-324 | ~1.8E+308 | 0.0 | `double d = 3.14159;` |
| `char` | 16 bits | '\u0000' (0) | '\uffff' (65,535) | '\u0000' | `char c = 'A';` |
| `boolean` | 1 bit* | — | — | false | `boolean flag = true;` |

> *The JVM typically uses 32 bits to store a boolean; 1 bit is the logical size.

---

## Code Examples for Each Type

```java
public class PrimitiveTypes {
    public static void main(String[] args) {
        // Whole numbers
        byte age = 25;
        short year = 2024;
        int population = 1_400_000_000;    // underscores improve readability
        long nationalDebt = 33_000_000_000_000L;  // L suffix required

        // Decimal numbers
        float gpa = 3.85f;     // f suffix required
        double pi = 3.141592653589793;

        // Single character
        char grade = 'A';
        char copyrightSymbol = '\u00A9';   // Unicode escape

        // True/false
        boolean isJavaFun = true;
        boolean isExamOver = false;

        System.out.println("Age: " + age);
        System.out.println("Year: " + year);
        System.out.println("Population: " + population);
        System.out.println("National debt: " + nationalDebt);
        System.out.println("GPA: " + gpa);
        System.out.println("Pi: " + pi);
        System.out.println("Grade: " + grade);
        System.out.println("Copyright: " + copyrightSymbol);
        System.out.println("Java fun: " + isJavaFun);
    }
}
```

---

## Common Mistakes with Primitives

**Forgetting the `L` suffix for `long`:**
```java
long big = 10_000_000_000;   // ❌ compile error: integer too large
long big = 10_000_000_000L;  // ✅ correct
```

**Forgetting the `f` suffix for `float`:**
```java
float price = 9.99;    // ❌ compile error: incompatible types (double to float)
float price = 9.99f;   // ✅ correct
```

**Integer overflow — silent and dangerous:**
```java
int max = Integer.MAX_VALUE;   // 2,147,483,647
int overflow = max + 1;        // wraps around to -2,147,483,648!
System.out.println(overflow);  // -2147483648 — no error, just wrong
```

💭 **Why does this happen?** An int is stored in 32 bits. Adding 1 to the maximum value causes the bits to wrap around to the most negative value. When you need numbers larger than ~2 billion, use `long`.

---

## Type Casting

Sometimes you need to convert a value from one type to another.

### Widening Cast (safe, automatic)
Moving to a larger type — no data is lost. Java does this automatically.

```java
int i = 100;
long l = i;      // int → long, automatic
double d = i;    // int → double, automatic
System.out.println(d); // 100.0
```

### Narrowing Cast (manual, may lose data)
Moving to a smaller type — you must cast explicitly, and you may lose data.

```java
double price = 9.99;
int truncated = (int) price;    // explicitly cast: decimal part is dropped (not rounded)
System.out.println(truncated);  // 9

long bigNumber = 10_000_000_000L;
int smaller = (int) bigNumber;  // data loss — value will be wrong
System.out.println(smaller);    // some garbage value
```

**Widening hierarchy** (safe to convert left-to-right automatically):
```
byte → short → int → long → float → double
                              ↑
                             char
```

---

## Primitive vs Reference Types

| Aspect | Primitive | Reference |
|--------|-----------|-----------|
| Stores | The actual value | A memory address (pointer to object on heap) |
| Memory location | Stack | Object on heap; reference on stack |
| Default value | 0, 0.0, false, '\u0000' | `null` |
| Comparison with `==` | Compares values | Compares references (addresses) — rarely what you want |
| Examples | `int`, `double`, `boolean` | `String`, `ArrayList`, any class you write |
| Wrapper class | Yes (`Integer`, `Double`, etc.) | Is already an object |

```java
int a = 5;
int b = 5;
System.out.println(a == b);       // true — same value

String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);     // false — different objects in memory
System.out.println(s1.equals(s2)); // true — same content
```

---

## Wrapper Classes

Each primitive has a corresponding class in `java.lang`:

| Primitive | Wrapper |
|-----------|---------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

Wrapper classes are needed when you require an object (e.g. in a `List<Integer>`). They also provide useful utility methods.

```java
// Useful wrapper methods
int parsed = Integer.parseInt("42");        // String → int
String asString = Integer.toString(42);     // int → String
int max = Integer.MAX_VALUE;                // 2147483647
int min = Integer.MIN_VALUE;                // -2147483648
boolean isDigit = Character.isDigit('7');   // true
char upper = Character.toUpperCase('a');    // 'A'
double rounded = Math.round(3.7);           // 4
```

**Autoboxing and unboxing:**
```java
// Autoboxing: primitive → wrapper (automatic)
List<Integer> numbers = new ArrayList<>();
numbers.add(42);           // int 42 is autoboxed to Integer(42)

// Unboxing: wrapper → primitive (automatic)
int value = numbers.get(0); // Integer(42) is unboxed to int 42
```

---

## String — a Special Reference Type

`String` is not a primitive — it is a class. But Java gives it special treatment:
- You can create String literals without `new`: `String name = "Alice";`
- The `+` operator works for String concatenation
- Strings are immutable (see [Strings](../strings/README.md) for details)

```java
String language = "Java";
String version = "21";
String message = language + " " + version;  // concatenation
System.out.println(message);                // Java 21
System.out.println(message.length());       // 7
```

---

## Exercises

1. Declare a variable of each of the 8 primitive types with appropriate values. Print each one with a label.
2. Write a program that demonstrates integer overflow: start with `Integer.MAX_VALUE`, add 1, and print the result. Explain what happened in a comment.
3. Demonstrate widening and narrowing type casting. Cast a `double` with a decimal part to `int` and print both values. Cast an `int` to a `char` and print the resulting character.
4. Use `Integer.parseInt()` to convert the String `"2024"` to an int, then add 1 and print the result. Use `Integer.toString()` to convert it back to a String and print the class of the result using `result.getClass().getSimpleName()`.
5. Write a program that shows the maximum and minimum values of `int`, `long`, and `double` using their wrapper class constants. Then show the size of a `long` in bits: `Long.SIZE`.

---

## Key Takeaways

- Java has 8 primitive types: `byte`, `short`, `int`, `long`, `float`, `double`, `char`, `boolean`
- `int` and `double` are the most commonly used
- Always add `L` to long literals and `f` to float literals
- Widening casts are automatic; narrowing casts require `(type)` and may lose data
- Use `.equals()` to compare reference types; `==` compares memory addresses
- Wrapper classes wrap primitives as objects and provide utility methods

---

## Related Topics

- [Variables](../variables/README.md) — declaring variables
- [Operators](../operators/README.md) — arithmetic and comparison
- [Strings](../strings/README.md) — the most common reference type
