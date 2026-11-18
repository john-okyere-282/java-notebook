# Variables

## What is a Variable?

A variable is a named storage location in memory that holds a value your program can use and change. Think of it like a labelled box: the label is the variable name, and what is inside the box is the value.

**Real-world analogy:** Imagine a hotel key cabinet. Each slot has a room number (the name) and holds the key (the value). You can take the key out (read the value) or put a new key in (change the value). The slot's label never changes, but its content can.

## Why Variables Matter

Without variables, you cannot store information between steps. Variables let your program remember things: a user's name, a running total, a game score, the current temperature. Every non-trivial program uses them constantly.

---

## Declaring a Variable in Java

```java
// Syntax: type variableName = value;
int age = 25;
String name = "John";
double salary = 45000.50;
boolean isActive = true;
```

Each part has a role:
- `int` — the **type** (what kind of data this variable stores)
- `age` — the **name** (how you refer to it in your code)
- `= 25` — the **assignment** (the initial value)
- `;` — ends the statement

---

## Declaration vs Initialization

```java
int score;         // declaration — reserves memory, but no value yet
score = 100;       // initialization — assigns the first value
int level = 1;     // declaration + initialization on one line (most common)
```

If you try to use a declared-but-not-initialised local variable, the compiler will refuse:
```java
int total;
System.out.println(total);  // ❌ compile error: variable total might not have been initialized
```

---

## Naming Rules

**Hard rules (the compiler enforces these):**
- Must start with a letter, underscore (`_`), or dollar sign (`$`)
- Cannot be a Java reserved keyword (`int`, `class`, `public`, `for`, etc.)
- Case-sensitive: `age`, `Age`, and `AGE` are three different variables

**Amalitech standards (§3.1, §3.5):**
- Use `lowerCamelCase`: `firstName`, `totalScore`, `isAvailable`
- Never start with `_` or `$`
- No Hungarian notation: `strName`, `iCount` are banned
- Names must reveal intent — if the name needs a comment to explain it, rename it

```java
// ✅ Good names (Amalitech §3.1)
int studentCount = 30;
String firstDayOfWeek = "Monday";
double accountBalance = 1500.00;
boolean isEnrolled = true;

// ❌ Bad names (Amalitech §3.1)
int x = 30;              // meaningless
String d = "Monday";     // needs a comment to explain it
double ab = 1500.00;     // unclear abbreviation
String strName = "Bob";  // Hungarian notation — banned
```

---

## Variable Types Overview

Java variables fall into two broad categories:

| Category | Stores | Examples | Lives in |
|----------|--------|----------|----------|
| **Primitive** | The actual value | `int`, `double`, `boolean`, `char` | Stack |
| **Reference** | A reference (address) to an object | `String`, `ArrayList`, any class | Heap |

The full details of each type are in [Data Types](../data-types/README.md).

---

## Constants — Variables That Never Change

If a variable's value should never change after assignment, declare it `final`. By convention, constants use `UPPER_SNAKE_CASE`.

```java
final double PI = 3.14159265358979;
final int MAX_LOGIN_ATTEMPTS = 3;
final String APP_NAME = "AmaliTrack";

// PI = 3.0;  // ❌ compile error: cannot assign a value to final variable PI
```

---

## Code Example — Basic

```java
public class VariableDemo {
    public static void main(String[] args) {
        String playerName = "Alice";
        int score = 0;
        boolean gameOver = false;

        score = score + 100;

        System.out.println("Player: " + playerName);
        System.out.println("Score: " + score);
        System.out.println("Game over: " + gameOver);
    }
}
```

**Expected output:**
```
Player: Alice
Score: 100
Game over: false
```

💭 **Question:** What happens if you try to print `score` before the line `score = score + 100;`? Try it.

---

## Code Example — Intermediate

```java
public class ShoppingCart {
    public static void main(String[] args) {
        String item1 = "Java Book";
        double price1 = 45.99;
        int quantity1 = 2;

        String item2 = "USB Drive";
        double price2 = 12.50;
        int quantity2 = 3;

        double total = (price1 * quantity1) + (price2 * quantity2);

        System.out.println("--- Shopping Cart ---");
        System.out.println(item1 + " x" + quantity1 + " = $" + (price1 * quantity1));
        System.out.println(item2 + " x" + quantity2 + " = $" + (price2 * quantity2));
        System.out.printf("Total: $%.2f%n", total);
    }
}
```

**Expected output:**
```
--- Shopping Cart ---
Java Book x2 = $91.98
USB Drive x3 = $37.5
Total: $129.48
```

---

## Common Mistakes

| Mistake | Example | Fix |
|---------|---------|-----|
| Using before initialising | `int x; System.out.println(x);` | Assign a value before using |
| Wrong type | `int age = "twenty";` | Match type to value |
| Comparing Strings with `==` | `if (name == "Alice")` | Use `name.equals("Alice")` |
| Hungarian notation | `String strFirstName` | Use `firstName` |
| Multiple declarations on one line | `int a, b, c;` (Amalitech §11.1) | Use three separate lines |

---

## Best Practices (Amalitech Standards)

- Choose names that reveal intent (§3.1). If you need a comment to explain the variable's purpose, the name is wrong.
- Declare variables close to where they are first used (§7.4). Do not declare everything at the top of a method.
- One declaration per line (§11.1). Write `int a;` then `int b;` on separate lines — not `int a, b;`.
- Use `final` for values that never change. It signals intent and helps the compiler optimise.

---

## Exercises

1. Declare variables for a person: name (`String`), age (`int`), height (`double`), isStudent (`boolean`). Print them all on separate lines.
2. Swap two variables (`a = 5`, `b = 10`) so `a` becomes `10` and `b` becomes `5`. Print the values before and after the swap.
3. Calculate and print the area and perimeter of a rectangle given `width = 8` and `height = 5`.
4. Declare a `price` (double) and a `quantity` (int). Calculate and print the total cost with 2 decimal places using `printf`.
5. Declare your name, your role (`"Systems Engineer"`), and years of experience. Print a one-line introduction: "My name is X, I am a Systems Engineer with Y years of experience."

---

## Key Takeaways

- Variables store values in named memory locations
- Declare with: `type variableName = value;`
- Use `lowerCamelCase` names that describe what the variable holds
- Java is statically typed — once you declare a type, it cannot change
- Use `final` for values that must not change

---

## Related Topics

- [Data Types](../data-types/README.md) — what types are available and their ranges
- [Operators](../operators/README.md) — how to use variables in calculations
- [Scope](../../03-methods/scope/README.md) — where variables can be accessed
