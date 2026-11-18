# Parameters and Return Types

## Parameters vs Arguments

These two words are often confused:

- **Parameter:** the variable declared in the method signature (a placeholder).
- **Argument:** the actual value you pass in when calling the method.

```java
// temperature is the PARAMETER (placeholder in the definition)
public static double celsiusToFahrenheit(double temperature) {
    return (temperature * 9.0 / 5.0) + 32;
}

// 100.0 is the ARGUMENT (the actual value passed in)
double boiling = celsiusToFahrenheit(100.0);
```

---

## Return Types

Every method must declare what type it returns. If it returns nothing, use `void`.

| Return type | Example | Meaning |
|-------------|---------|---------|
| `void` | `public static void printHello()` | Returns nothing |
| `int` | `public static int add(int a, int b)` | Returns an integer |
| `double` | `public static double average(...)` | Returns a decimal |
| `String` | `public static String greet(String name)` | Returns text |
| `boolean` | `public static boolean isEven(int n)` | Returns true/false |
| Any class | `public static Scanner getScanner()` | Returns an object |

```java
public static boolean isAdult(int age) {
    return age >= 18;
}

public static String getCategory(int score) {
    if (score >= 90) return "Excellent";
    if (score >= 70) return "Good";
    return "Needs improvement";
}

public static double average(int a, int b, int c) {
    return (a + b + c) / 3.0;  // 3.0 forces floating-point division
}
```

> **Important:** Every execution path in a non-void method must return a value. The compiler enforces this.

---

## Pass by Value in Java

Java always passes **primitive** values by copy. The method works on a copy — it cannot change the caller's variable.

```java
public static void tryToChange(int x) {
    x = 999;   // changes the COPY, not the original
    System.out.println("Inside method: " + x);  // 999
}

public static void main(String[] args) {
    int number = 42;
    tryToChange(number);
    System.out.println("After method: " + number);  // still 42!
}
```

### Objects: the reference is passed by value

For objects (reference types), Java passes a **copy of the reference**. The method can modify the object's internal state, but it cannot change which object the caller's variable points to.

```java
import java.util.ArrayList;
import java.util.List;

public static void addItem(List<String> list) {
    list.add("added");     // modifies the OBJECT (visible to caller)
    list = new ArrayList<>();  // reassigns local copy (not visible to caller)
}

public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("Alice");
    addItem(names);
    System.out.println(names);  // [Alice, added]  — the add() was visible
                                // the reassignment was NOT
}
```

---

## Multiple Parameters

```java
public static double rectangleArea(double width, double height) {
    return width * height;
}

public static String formatName(String firstName, String lastName) {
    return lastName + ", " + firstName;
}
```

> **Amalitech §4.3:** Keep parameters few. One or two is ideal. At three, consider whether a small record or object would be cleaner. At four or more, refactor.

---

## Early Returns for Clarity

You can return from a method at any point. Using early returns for guard clauses keeps the main logic unindented and readable:

```java
// Hard to read — deeply nested
public static String classify(int n) {
    if (n > 0) {
        if (n % 2 == 0) {
            return "positive even";
        } else {
            return "positive odd";
        }
    } else {
        return "not positive";
    }
}

// Cleaner with early return
public static String classify(int n) {
    if (n <= 0) return "not positive";
    if (n % 2 == 0) return "positive even";
    return "positive odd";
}
```

---

## Exercises

**Exercise 1:** Write a method `max(int a, int b)` that returns the larger of the two numbers. Test it with several pairs.

**Exercise 2:** Write a method `isLeapYear(int year)` that returns `true` if the year is a leap year. (Divisible by 4, except centuries unless also divisible by 400.)

**Exercise 3:** Write a method `initials(String firstName, String lastName)` that returns a String like `"J.O."`.

**Exercise 4:** Write a method `hypotenuse(double a, double b)` that returns the length of the hypotenuse of a right triangle using `Math.sqrt(a*a + b*b)`.

**Exercise 5:** Write a method `describe(int n)` that returns `"negative"`, `"zero"`, `"small positive"` (1–9), or `"large positive"` (10+). Demonstrate the pass-by-value principle by writing a second method that tries to change an `int` parameter and showing in `main()` that the original is unchanged.
