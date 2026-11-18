# Method Overloading

## What is Overloading?
Method overloading means defining multiple methods with the **same name** but **different parameter lists** in the same class. Java picks the right version based on the argument types at compile time — this is called **compile-time polymorphism**.

```java
public class Calculator {

    // Three versions of add()
    public static int add(int a, int b) {
        return a + b;
    }

    public static double add(double a, double b) {
        return a + b;
    }

    public static int add(int a, int b, int c) {
        return a + b + c;
    }

    public static void main(String[] args) {
        System.out.println(add(2, 3));       // 5    — calls int version
        System.out.println(add(2.5, 3.1));   // 5.6  — calls double version
        System.out.println(add(1, 2, 3));    // 6    — calls 3-param version
    }
}
```

---

## Rules for Overloading

Methods are considered different overloads if they differ in at least one of these:
1. **Number of parameters** (`add(int, int)` vs `add(int, int, int)`)
2. **Types of parameters** (`add(int, int)` vs `add(double, double)`)
3. **Order of parameter types** (`process(int, String)` vs `process(String, int)`)

**Return type alone does NOT create a different overload** — this is a compile error:
```java
// DOES NOT COMPILE — same name and parameters, only return type differs
public static int getValue() { return 1; }
public static double getValue() { return 1.0; }  // Error!
```

Parameter names also do not matter to the compiler:
```java
// These are the SAME method (compile error — duplicate)
public static int add(int x, int y) { return x + y; }
public static int add(int a, int b) { return a + b; }  // Error!
```

---

## Real Use Case — Flexible Formatting

```java
public class Formatter {

    public static String format(int number) {
        return String.valueOf(number);
    }

    public static String format(double number) {
        return String.format("%.2f", number);
    }

    public static String format(boolean flag) {
        return flag ? "Yes" : "No";
    }

    public static String format(String text) {
        return "\"" + text + "\"";
    }

    public static void main(String[] args) {
        System.out.println(format(42));        // 42
        System.out.println(format(3.14159));   // 3.14
        System.out.println(format(true));      // Yes
        System.out.println(format("hello"));   // "hello"
    }
}
```

This is much cleaner than naming them `formatInt()`, `formatDouble()`, `formatBoolean()`. Callers don't need to know which version to use — Java figures it out.

---

## Standard Library Examples

Java's own libraries use overloading extensively:
- `System.out.println(int)`, `println(double)`, `println(String)`, `println(boolean)` — all different overloads.
- `Math.abs(int)`, `Math.abs(long)`, `Math.abs(double)` — same method name, three overloads.
- `String.valueOf(int)`, `String.valueOf(char)`, `String.valueOf(boolean)` — six overloads total.

---

## When Overloading Makes Sense
Use overloading when:
- Multiple methods do the same logical thing on different types of input.
- You want a consistent API without forcing callers to remember different method names.

Avoid overloading when the methods do different things — use distinct names instead. Overloading a method with two versions that behave very differently is confusing.

---

## Exercises

**Exercise 1:** Write overloaded `multiply()` methods: one that multiplies two `int`s, one that multiplies two `double`s, and one that repeats a `String` a given number of times. Test all three.

**Exercise 2:** Write overloaded `describe()` methods: one for `int` (prints "The number is X"), one for `String` (prints "The text is: X"), and one for `boolean` (prints "The flag is on/off"). Test all three from `main()`.

**Exercise 3:** Write overloaded `area()` methods: one for a circle (takes a `double` radius), one for a rectangle (takes `double` width and `double` height), and one for a triangle (takes `double` base and `double` height). All return a `double`.
