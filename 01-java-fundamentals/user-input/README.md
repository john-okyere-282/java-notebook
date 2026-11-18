# User Input with Scanner

## What is Scanner?

`Scanner` is a class in `java.util` that reads input from various sources: the keyboard (`System.in`), a file, or a string. The most common use in beginner Java programs is reading keyboard input.

You must import it before use:
```java
import java.util.Scanner;
```

---

## Basic Usage

```java
import java.util.Scanner;

public class InputDemo {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter your name: ");
        String name = scanner.nextLine();

        System.out.print("Enter your age: ");
        int age = scanner.nextInt();

        System.out.println("Hello, " + name + "! You are " + age + " years old.");

        scanner.close();  // always close the scanner when done
    }
}
```

**Sample session:**
```
Enter your name: Alice
Enter your age: 28
Hello, Alice! You are 28 years old.
```

---

## Scanner Methods

| Method | Reads | Returns |
|--------|-------|---------|
| `nextLine()` | Entire line (including spaces) until Enter | `String` |
| `next()` | One word (stops at whitespace) | `String` |
| `nextInt()` | An integer | `int` |
| `nextDouble()` | A decimal number | `double` |
| `nextLong()` | A long integer | `long` |
| `nextBoolean()` | `true` or `false` (case-insensitive) | `boolean` |
| `hasNextInt()` | Checks if next token is an int | `boolean` |
| `hasNextLine()` | Checks if another line exists | `boolean` |

```java
Scanner sc = new Scanner(System.in);

System.out.print("Enter a double: ");
double d = sc.nextDouble();

System.out.print("Enter an int: ");
int i = sc.nextInt();

System.out.print("Enter a boolean: ");
boolean b = sc.nextBoolean();

System.out.println(d + " " + i + " " + b);
sc.close();
```

---

## Common Pitfall: `nextInt()` + `nextLine()`

This trips up almost every beginner. `nextInt()` reads the integer but leaves the newline character (`\n`) in the input buffer. The next `nextLine()` call reads that leftover newline instead of waiting for real input.

```java
Scanner sc = new Scanner(System.in);

System.out.print("Enter age: ");
int age = sc.nextInt();

// Without the fix below, this nextLine() reads the leftover newline immediately
// and the user never gets to type their name!
sc.nextLine();  // ← consume the leftover newline

System.out.print("Enter name: ");
String name = sc.nextLine();  // now waits for real input

System.out.println(name + " is " + age + " years old.");
sc.close();
```

**Rule:** After any `nextInt()`, `nextDouble()`, or `nextLong()`, add a `sc.nextLine();` call to clear the buffer before the next `nextLine()`.

---

## Reading Multiple Values on One Line

If the user enters values separated by spaces, `next()` reads them one at a time:

```java
Scanner sc = new Scanner(System.in);
System.out.print("Enter three numbers separated by spaces: ");
int x = sc.nextInt();
int y = sc.nextInt();
int z = sc.nextInt();
System.out.println("Sum: " + (x + y + z));
sc.close();
```

---

## Input Validation

If the user types something unexpected (e.g. types "hello" when you call `nextInt()`), Scanner throws `InputMismatchException`. Use `hasNextInt()` to check first:

```java
import java.util.Scanner;
import java.util.InputMismatchException;

public class ValidatedInput {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter a whole number: ");
        while (!sc.hasNextInt()) {
            System.out.println("That's not a valid integer. Please try again: ");
            sc.next();  // discard the invalid token
        }
        int number = sc.nextInt();
        System.out.println("You entered: " + number);

        sc.close();
    }
}
```

---

## Practical Example — Employee Data Entry

```java
import java.util.Scanner;

public class EmployeeEntry {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Employee name: ");
        String name = sc.nextLine();

        System.out.print("Department: ");
        String department = sc.nextLine();

        System.out.print("Years of service: ");
        int years = sc.nextInt();

        System.out.print("Annual salary: ");
        double salary = sc.nextDouble();

        sc.close();

        System.out.println("\n--- Employee Record ---");
        System.out.printf("Name:       %s%n", name);
        System.out.printf("Dept:       %s%n", department);
        System.out.printf("Service:    %d years%n", years);
        System.out.printf("Salary:     $%,.2f/year%n", salary);
        System.out.printf("Monthly:    $%,.2f/month%n", salary / 12);
    }
}
```

**Sample output:**
```
--- Employee Record ---
Name:       John Okyere
Dept:       Systems Engineering
Service:    2 years
Salary:     $55,000.00/year
Monthly:    $4,583.33/month
```

---

## Closing the Scanner

Always call `scanner.close()` when you are done. This releases the underlying resource. A better pattern uses try-with-resources (covered in Phase 06):

```java
try (Scanner sc = new Scanner(System.in)) {
    System.out.print("Enter something: ");
    String input = sc.nextLine();
    System.out.println("You said: " + input);
}  // sc.close() is called automatically here
```

**Note:** Closing a Scanner that wraps `System.in` also closes `System.in`, which means you cannot read more input in the same program run. For simple programs this is fine; for complex programs, leave `System.in` open.

---

## Exercises

1. Write a program that asks the user for their first name, last name, and favourite colour, then prints: "Hello! My name is [First] [Last] and I like [colour]."
2. Write a program that reads two integers and prints their sum, difference, product, and quotient (as a `double`).
3. Write a temperature converter: ask the user to enter a temperature in Celsius and print the equivalent in Fahrenheit (`F = C * 9.0/5 + 32`) and Kelvin (`K = C + 273.15`), both to 2 decimal places.
4. Write a program that reads a number and uses `hasNextInt()` validation — keep prompting until the user enters a valid integer. Then print whether the number is positive, negative, or zero.
5. *(Challenge)* Write a simple quiz. Ask the user three questions about Java (e.g. "What does JDK stand for?"). Accept their answer, compare it to the correct answer (case-insensitive using `.equalsIgnoreCase()`), and print a final score out of 3.

---

## Key Takeaways

- Import `java.util.Scanner` and pass `System.in` to read keyboard input
- Call `scanner.close()` when finished
- Use the right method for the right type: `nextLine()` for strings, `nextInt()` for integers, `nextDouble()` for decimals
- After `nextInt()` / `nextDouble()`, consume the leftover newline with `sc.nextLine()` before the next `nextLine()` call
- Validate input with `hasNextInt()` / `hasNextDouble()` to avoid crashes

---

## Related Topics

- [Variables](../variables/README.md) — storing the input you read
- [Data Types](../data-types/README.md) — choosing the right Scanner method for the right type
- [Exception Handling](../../06-exception-handling/README.md) — handling `InputMismatchException` properly
