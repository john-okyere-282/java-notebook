# switch

## What is switch?
`switch` lets you compare one variable against many fixed values and run the matching block. It is cleaner than a long else-if chain when you are testing a single variable against specific values.

## Traditional switch Statement
```java
int day = 3;
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    case 3:
        System.out.println("Wednesday");
        break;
    default:
        System.out.println("Another day");
}
```

## Fall-Through Behaviour
Without `break`, execution falls through to the next case. This is usually a bug, but sometimes useful for grouping cases:

```java
// Fall-through to group cases
switch (day) {
    case 1: case 2: case 3: case 4: case 5:
        System.out.println("Weekday");
        break;
    case 6: case 7:
        System.out.println("Weekend");
        break;
    default:
        System.out.println("Invalid day");
}
```

> ⚠️ **Warning:** Always add `break` at the end of each case unless you intentionally want fall-through. Missing `break` is one of the most common bugs in Java.

## Switch Expressions (Java 14+) — Arrow Syntax
Modern, concise syntax with no fall-through risk:

```java
String dayName = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    case 4 -> "Thursday";
    case 5 -> "Friday";
    case 6 -> "Saturday";
    case 7 -> "Sunday";
    default -> "Invalid day";
};
System.out.println(dayName);
```

Key differences from the traditional form:
- Uses `->` instead of `:` and `break`
- Each arrow case is a single expression — no fall-through
- The whole thing is an expression that produces a value
- `default` is required when not all values are covered

## switch with yield (multi-line blocks)
When a case needs multiple statements, use a block with `yield` to return a value:

```java
int points = switch (grade) {
    case "A" -> 4;
    case "B" -> 3;
    case "C" -> {
        System.out.println("Average grade");
        yield 2;
    }
    case "D" -> 1;
    default -> 0;
};
```

## switch on Strings and Enums
Java's `switch` works on these types:
- `int`, `short`, `byte`, `char` (and their wrapper types)
- `String` (Java 7+)
- `enum` types

```java
// switch on String
String season = "SUMMER";
switch (season) {
    case "SPRING" -> System.out.println("Flowers bloom");
    case "SUMMER" -> System.out.println("Hot days");
    case "AUTUMN" -> System.out.println("Leaves fall");
    case "WINTER" -> System.out.println("Cold nights");
    default       -> System.out.println("Unknown season");
}
```

## switch vs if-else — When to Use Which

| Situation | Use |
|-----------|-----|
| Single variable vs fixed values (4+ options) | `switch` |
| Range checks (`score >= 90`) | `if-else` |
| Complex boolean conditions | `if-else` |
| Multiple variables combined | `if-else` |
| Cleaner, modern code (Java 14+) | switch expression |

## Practical Example
```java
public class GradeDescriber {
    public static void main(String[] args) {
        String grade = "B";

        String description = switch (grade) {
            case "A" -> "Excellent — keep it up!";
            case "B" -> "Good work.";
            case "C" -> "Satisfactory — room to improve.";
            case "D" -> "Below average — seek help.";
            case "F" -> "Failing — retake required.";
            default  -> "Unknown grade: " + grade;
        };

        System.out.println(description);
    }
}
```
Output: `Good work.`

## Exercises

**Exercise 1:** Write a program that takes a month number (1–12) and prints the month name using a traditional `switch`.

**Exercise 2:** Rewrite Exercise 1 using the modern arrow-syntax switch expression.

**Exercise 3:** Write a simple calculator. Take two numbers and an operator (`+`, `-`, `*`, `/`) as input. Use `switch` to perform and print the result.

**Exercise 4:** Given a day number (1 = Monday … 7 = Sunday), use `switch` to print whether it is a weekday or weekend.

**Exercise 5:** Write a program that takes a season name and uses `switch` to describe the typical weather for that season.
