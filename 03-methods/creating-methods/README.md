# Creating and Calling Methods

## Method Anatomy

Every method in Java has the same basic structure:

```java
// accessModifier  returnType  methodName  (parameterList) {
//     method body
//     return value;  // required if returnType is not void
// }

public static int addNumbers(int a, int b) {
    return a + b;
}
```

| Part | Example | Meaning |
|------|---------|---------|
| Access modifier | `public` | Who can call this method |
| `static` keyword | `static` | Belongs to the class, not an object |
| Return type | `int` | What type of value the method gives back |
| Method name | `addNumbers` | Verb or verb phrase describing what it does |
| Parameters | `int a, int b` | Inputs the method needs to do its work |
| Body | `{ return a + b; }` | The actual logic |
| `return` statement | `return a + b;` | Sends the result back to the caller |

---

## Calling a Method

```java
public class MathHelper {
    public static void main(String[] args) {
        int result = addNumbers(10, 5);       // calling the method
        System.out.println("Sum: " + result); // Output: Sum: 15

        // You can also use the return value directly
        System.out.println("Double sum: " + addNumbers(result, result));
    }

    public static int addNumbers(int a, int b) {
        return a + b;
    }
}
```

Execution flow:
1. `main()` starts.
2. `addNumbers(10, 5)` is called — execution jumps into `addNumbers`.
3. Inside `addNumbers`, `a = 10`, `b = 5`, result is `15`.
4. `return 15` sends execution back to `main()`.
5. `result` is assigned `15`.

---

## void Methods (no return value)

A method with return type `void` performs an action but does not give back a value.

```java
public static void printWelcome(String name) {
    System.out.println("Welcome, " + name + "!");
    System.out.println("Glad you're here.");
}

// Called like this — no variable to store the result
printWelcome("John");
// Output:
// Welcome, John!
// Glad you're here.
```

> You can use a bare `return;` inside a `void` method to exit early, but you don't have to include one at the end.

---

## A Complete Example

```java
public class TemperatureConverter {

    public static void main(String[] args) {
        double boiling  = celsiusToFahrenheit(100.0);
        double freezing = celsiusToFahrenheit(0.0);
        double body     = celsiusToFahrenheit(37.0);

        printTemperature("Boiling point", boiling);
        printTemperature("Freezing point", freezing);
        printTemperature("Body temperature", body);
    }

    // Converts Celsius to Fahrenheit and returns the result
    public static double celsiusToFahrenheit(double celsius) {
        return (celsius * 9.0 / 5.0) + 32;
    }

    // Prints a labelled temperature in a consistent format
    public static void printTemperature(String label, double fahrenheit) {
        System.out.printf("%s: %.1f°F%n", label, fahrenheit);
    }
}
// Output:
// Boiling point: 212.0°F
// Freezing point: 32.0°F
// Body temperature: 98.6°F
```

Notice how `main()` reads almost like plain English — it describes **what** happens, not **how**. The `how` is in each method.

---

## Amalitech Standards for Methods (§4)

> 📖 **Official Amalitech Standard:** See §4 of the Amalitech Coding Standards for full method guidelines.

**§4.1 — Keep methods short.** Methods should rarely exceed 20 lines. If a method is getting long, it is trying to do too much — split it.

**§4.2 — One job per method.** A method named `calculateTotal()` should calculate the total — not also print it, save it, and log it.

**§4.3 — Few parameters.** One or two parameters is ideal. At three or more, consider whether some parameters belong together as an object. At four or more, strongly consider refactoring.

**Naming:** Use verb or verb-phrase names that describe the action:
- `calculateTotal()` ✅
- `isValid()` ✅
- `sendEmail()` ✅
- `data()` ❌ (noun — what does this do?)
- `doStuff()` ❌ (meaningless)

---

## Exercises

**Exercise 1:** Write a method `square(int n)` that returns `n * n`. Call it from `main()` for the values 1 through 10 and print the results.

**Exercise 2:** Write a `void` method `printSeparator()` that prints a line of 40 dashes. Call it between sections of output.

**Exercise 3:** Write a method `greet(String name, String timeOfDay)` that prints `"Good morning, Alice!"` or `"Good evening, Bob!"` depending on the `timeOfDay` argument.

**Exercise 4:** Write a method `clamp(int value, int min, int max)` that returns `min` if value < min, `max` if value > max, or `value` otherwise. Test it with several inputs.

**Exercise 5:** Write a method `printMultiplesOf(int n, int limit)` that prints all multiples of `n` up to `limit`. It is `void` — it just prints.
