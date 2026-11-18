# Operators

## What are Operators?

Operators are symbols that perform operations on one or more values (operands) and produce a result. Java has operators for arithmetic, comparison, logic, assignment, and more.

---

## Arithmetic Operators

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `+` | Addition | `10 + 3` | `13` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `10 * 3` | `30` |
| `/` | Division | `10 / 3` | `3` (integer division!) |
| `%` | Modulo (remainder) | `10 % 3` | `1` |

```java
int a = 10;
int b = 3;
System.out.println(a + b);  // 13
System.out.println(a - b);  // 7
System.out.println(a * b);  // 30
System.out.println(a / b);  // 3  — integer division drops the decimal
System.out.println(a % b);  // 1  — remainder of 10 ÷ 3
```

**⚠️ Integer division:** When both operands are integers, Java discards the decimal part. `10 / 3` is `3`, not `3.333`. To get the full decimal, cast at least one operand to `double`:

```java
double result = (double) 10 / 3;
System.out.println(result);  // 3.3333333333333335
```

**Modulo use cases:**
- Check if a number is even: `n % 2 == 0`
- Wrap an index around: `index % arrayLength`
- Extract the last digit of a number: `number % 10`

---

## Comparison Operators

Comparison operators return a `boolean` (`true` or `false`).

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `==` | Equal to | `5 == 5` | `true` |
| `!=` | Not equal to | `5 != 3` | `true` |
| `<` | Less than | `3 < 5` | `true` |
| `>` | Greater than | `5 > 3` | `true` |
| `<=` | Less than or equal | `5 <= 5` | `true` |
| `>=` | Greater than or equal | `4 >= 5` | `false` |

```java
int x = 10;
int y = 20;
System.out.println(x == y);   // false
System.out.println(x != y);   // true
System.out.println(x < y);    // true
System.out.println(x > y);    // false
System.out.println(x >= 10);  // true
```

**⚠️ Warning — never use `==` to compare Strings:**
```java
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);      // false — compares references, not content
System.out.println(s1.equals(s2)); // true  — compares content
```

---

## Logical Operators

Logical operators combine boolean expressions.

| Operator | Meaning | Returns `true` when |
|----------|---------|-------------------|
| `&&` | AND | Both sides are `true` |
| `\|\|` | OR | At least one side is `true` |
| `!` | NOT | The operand is `false` |

**Truth table:**

| A | B | A && B | A \|\| B | !A |
|---|---|--------|---------|-----|
| true | true | true | true | false |
| true | false | false | true | false |
| false | true | false | true | true |
| false | false | false | false | true |

```java
int age = 25;
boolean hasID = true;

// AND: both conditions must be true
boolean canEnter = (age >= 18) && hasID;
System.out.println(canEnter);   // true

// OR: at least one must be true
boolean isDayOff = (age < 18) || !hasID;
System.out.println(isDayOff);   // false

// NOT: flips the value
System.out.println(!hasID);     // false
```

**Short-circuit evaluation:**  
With `&&`, if the left side is `false`, Java skips evaluating the right side — the result is already `false`.  
With `||`, if the left side is `true`, Java skips evaluating the right side — the result is already `true`.  
This matters when the right side has side effects or could throw an exception:

```java
String name = null;
// Without short-circuit, calling .isEmpty() on null would crash
if (name != null && name.isEmpty()) {
    System.out.println("Name is empty");
}
// name != null is false → Java never evaluates name.isEmpty() → no crash
```

---

## Assignment Operators

| Operator | Meaning | Equivalent to |
|----------|---------|--------------|
| `=` | Assign | — |
| `+=` | Add and assign | `x = x + 5` |
| `-=` | Subtract and assign | `x = x - 5` |
| `*=` | Multiply and assign | `x = x * 5` |
| `/=` | Divide and assign | `x = x / 5` |
| `%=` | Modulo and assign | `x = x % 5` |

```java
int score = 100;
score += 50;    // score = 150
score -= 20;    // score = 130
score *= 2;     // score = 260
score /= 4;     // score = 65
score %= 10;    // score = 5
System.out.println(score); // 5
```

---

## Increment and Decrement

`++` adds 1; `--` subtracts 1. Where you place the operator matters:

```java
int n = 5;

// Prefix: modify FIRST, then use
System.out.println(++n);  // 6 — n becomes 6, then prints 6
System.out.println(n);    // 6

// Reset
n = 5;

// Postfix: use FIRST, then modify
System.out.println(n++);  // 5 — prints 5, then n becomes 6
System.out.println(n);    // 6
```

A simple memory aid: prefix (`++n`) means "increment now"; postfix (`n++`) means "increment after".

```java
// Common loop use — prefix and postfix have the same effect here
for (int i = 0; i < 5; i++) {   // i++ is fine
    System.out.print(i + " ");
}
// Output: 0 1 2 3 4
```

---

## Ternary Operator

A compact if-else expression: `condition ? valueIfTrue : valueIfFalse`

```java
int age = 20;
String status = age >= 18 ? "Adult" : "Minor";
System.out.println(status);  // Adult

int a = 10, b = 20;
int max = a > b ? a : b;
System.out.println("Max: " + max);  // Max: 20
```

Avoid nesting ternaries — they become unreadable. If you need nested conditions, use `if-else` instead.

---

## Operator Precedence

When an expression has multiple operators, Java applies them in a fixed order (high to low):

| Priority | Operators |
|----------|-----------|
| 1 (highest) | `++` `--` (postfix) |
| 2 | `++` `--` (prefix), `+` `-` (unary), `!` |
| 3 | `*` `/` `%` |
| 4 | `+` `-` (binary) |
| 5 | `<` `>` `<=` `>=` |
| 6 | `==` `!=` |
| 7 | `&&` |
| 8 | `\|\|` |
| 9 | `?:` (ternary) |
| 10 (lowest) | `=` `+=` `-=` etc. |

**Tip:** Use parentheses to make intent explicit and avoid relying on precedence rules:

```java
// Ambiguous
int result = 2 + 3 * 4;    // 14 — * before +

// Clear
int result = 2 + (3 * 4);  // 14 — intent obvious
```

---

## Common Mistakes

| Mistake | Example | Fix |
|---------|---------|-----|
| Integer division silently truncates | `double avg = total / count;` (both int) | Cast: `(double) total / count` |
| Confusing `=` with `==` | `if (x = 5)` | Use `if (x == 5)` |
| Using `==` on Strings | `if (s1 == s2)` | Use `s1.equals(s2)` |
| Missing parentheses in complex expressions | `a + b * c - d / e` | Add `()` to show grouping |

---

## Code Example — Combining Operators

```java
public class OperatorDemo {
    public static void main(String[] args) {
        int price = 100;
        int discount = 15;
        int quantity = 3;

        double finalPrice = (price - (price * discount / 100.0)) * quantity;
        System.out.printf("Final price: $%.2f%n", finalPrice);  // Final price: $255.00

        // Check eligibility using logical operators
        boolean isEmployee = true;
        boolean yearsOfService = 5 >= 3;  // true

        boolean getsBonus = isEmployee && yearsOfService;
        System.out.println("Gets bonus: " + getsBonus);  // Gets bonus: true

        // Ternary for a quick label
        String priceLabel = finalPrice > 200 ? "Premium" : "Standard";
        System.out.println("Price category: " + priceLabel);  // Price category: Premium
    }
}
```

---

## Exercises

1. Declare `int a = 15` and `int b = 4`. Print the result of every arithmetic operator (`+`, `-`, `*`, `/`, `%`). Note: explain in a comment why `a / b` is `3` and not `3.75`.
2. Demonstrate integer division loss. Declare two integers and show the difference between `int result = a / b` and `double result = (double) a / b`.
3. Use all six comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`) on two numbers and print each result with a descriptive label.
4. Write a program that determines whether a year is a leap year. A year is a leap year if it is divisible by 4, except for century years, which must be divisible by 400. Use `&&`, `||`, and `%`. Test with 2000, 1900, and 2024.
5. Using only arithmetic operators, swap two integers `a = 100` and `b = 200` without using a third variable. Use `a = a + b; b = a - b; a = a - b;`. Print before and after.
6. Write an expression using the ternary operator that takes a `score` (int) and prints `"Pass"` if score >= 50, otherwise `"Fail"`. Then extend it to also handle `"Distinction"` for score >= 80.

---

## Key Takeaways

- Arithmetic operators: `+` `-` `*` `/` `%`
- `/` on two integers performs integer division (truncates the decimal)
- Comparison operators return `boolean`; always use `.equals()` for Strings
- Logical operators: `&&` (AND), `||` (OR), `!` (NOT)
- Assignment shorthand: `+=`, `-=`, `*=`, `/=`, `%=`
- Use parentheses to make complex expressions readable

---

## Related Topics

- [Variables](../variables/README.md) — storing values
- [Data Types](../data-types/README.md) — type casting to avoid integer division issues
- [Control Flow](../../02-control-flow/if-statements/README.md) — using boolean expressions in conditions
