# Comments

## What are Comments?

Comments are lines in your source code that the compiler ignores completely. They exist for human readers — you, your teammates, and your future self — to explain or document the code.

The key question about every comment is: *does this add information the code itself cannot convey?* If not, it is noise.

---

## Types of Comments in Java

### Single-line comment

Starts with `//`. Everything from `//` to the end of the line is ignored.

```java
int retryLimit = 3;  // maximum number of login attempts before lockout

// Calculating compound interest
double amount = principal * Math.pow(1 + rate / n, n * time);
```

### Multi-line comment

Starts with `/*` and ends with `*/`. Can span any number of lines.

```java
/*
 * This block calculates the hash of a password before storing it.
 * We use SHA-256 because the partner authentication service requires
 * that specific algorithm for cross-system identity matching.
 */
String hashedPassword = sha256(rawPassword + salt);
```

### Javadoc comment

Starts with `/**` and ends with `*/`. Placed immediately before a public class, interface, or method. The `javadoc` tool reads these to generate HTML API documentation.

```java
/**
 * Calculates the monthly repayment for a fixed-rate loan.
 *
 * @param principal  the loan amount in cedis
 * @param annualRate the annual interest rate as a decimal (e.g. 0.12 for 12%)
 * @param months     the loan term in months
 * @return           the monthly repayment amount, rounded to 2 decimal places
 * @throws IllegalArgumentException if principal or months is not positive
 */
public double calculateMonthlyRepayment(double principal, double annualRate, int months) {
    if (principal <= 0 || months <= 0) {
        throw new IllegalArgumentException("Principal and months must be positive");
    }
    double monthlyRate = annualRate / 12;
    return principal * (monthlyRate / (1 - Math.pow(1 + monthlyRate, -months)));
}
```

**Common Javadoc tags:**

| Tag | Purpose |
|-----|---------|
| `@param name description` | Documents a parameter |
| `@return description` | Documents the return value |
| `@throws ExceptionType description` | Documents an exception |
| `@author name` | Documents the author |
| `@since version` | Documents when it was added |
| `@deprecated message` | Marks as deprecated |
| `{@code text}` | Inline code in doc |
| `@see ClassName#method` | Cross-reference |

---

## The Amalitech Standard on Comments

The Amalitech Coding Standards §10 gives clear guidance. The core principle: **comment the *why*, not the *what*.**

Good code is mostly self-documenting. If the code clearly shows *what* is happening, a comment that repeats it adds noise, not value.

### §10.1 — Comment the why, not the what

**❌ Bad — restates what the code already says:**
```java
// loop through every character
for (int i = 0; i < data.length(); i++) {
    // get the character at position i
    char c = data.charAt(i);
    // add c to the total
    total += c;
}
```

**✅ Good — explains business logic that isn't obvious:**
```java
for (int i = 0; i < data.length(); i++) {
    // Using a polynomial rolling hash to match the partner system's checksum algorithm.
    // This specific formula (hash * 31 + c) is required by the integration spec §4.2.
    hash = (hash << 5) - hash + data.charAt(i);
}
```

### §10.2 — No journal comments

Do not use code comments as a change log. That is what Git is for.

**❌ Bad:**
```java
// 2025-01-10 — John: added null check
// 2025-01-15 — Alice: refactored to use stream
// 2025-01-20 — John: reverted stream, too slow
public void processOrder(Order order) {
```

**✅ Good — just the code, history in Git:**
```java
public void processOrder(Order order) {
```

### §10.3 — No positional markers

Avoid banner comments used as visual dividers. Well-structured code with small, well-named methods does not need them.

**❌ Bad:**
```java
// ==============================
// GETTERS AND SETTERS
// ==============================
public String getName() { return name; }
```

**✅ Good:**
```java
public String getName() { return name; }
```

---

## When Comments ARE Appropriate

- **Business rules or regulatory requirements** that cannot be encoded in method names:  
  `// 30-day waiting period required by the Insurance Act 2024, section 12(b)`

- **Non-obvious algorithm steps** (mathematical formulas, encoding tricks):  
  `// Bit manipulation: shift left by 5 is equivalent to multiplying by 32`

- **Known limitations or TODOs** (but resolve them soon):  
  `// TODO: replace with async processing once Task #142 is complete`

- **Warnings about subtle side effects or thread safety concerns**

- **Javadoc on all public API methods and classes**

---

## TODO and FIXME Conventions

IntelliJ recognises `TODO` and `FIXME` tags and displays them in the **TODO panel** (View → Tool Windows → TODO).

```java
// TODO: implement pagination when the dataset exceeds 10,000 records
// FIXME: this calculation is wrong for leap years — fix before March release

public List<Employee> getAllEmployees() {
    return repository.findAll();
}
```

---

## Commented-Out Code

Never commit commented-out code. If you are removing something, delete it. Git history preserves everything — you can always retrieve it with `git log` or `git show`.

**❌ Bad:**
```java
// String oldMethod = legacyCalc(data);
// System.out.println("Debug: " + result);
String result = newCalc(data);
```

**✅ Good:**
```java
String result = newCalc(data);
```

---

## Code Example — Well-Commented Class

```java
/**
 * Calculates employee bonuses based on performance rating and years of service.
 *
 * <p>Bonus rules are defined in the HR Policy Document v3.2 (2024-Q4).
 * Any change to the multiplier table requires HR approval.
 */
public class BonusCalculator {

    // Multipliers per performance band — sourced from HR Policy §8.3
    private static final double EXCEEDS_MULTIPLIER = 0.20;
    private static final double MEETS_MULTIPLIER   = 0.12;
    private static final double BELOW_MULTIPLIER   = 0.05;

    /**
     * Calculates the annual bonus for an employee.
     *
     * @param salary      the employee's annual gross salary
     * @param rating      the performance rating: "EXCEEDS", "MEETS", or "BELOW"
     * @param yearsServed number of complete years of service
     * @return            the bonus amount; 0.0 if rating is unrecognised
     */
    public double calculateBonus(double salary, String rating, int yearsServed) {
        double baseMultiplier = switch (rating.toUpperCase()) {
            case "EXCEEDS" -> EXCEEDS_MULTIPLIER;
            case "MEETS"   -> MEETS_MULTIPLIER;
            case "BELOW"   -> BELOW_MULTIPLIER;
            default        -> 0.0;
        };

        // Senior employees (5+ years) receive an additional 2% loyalty uplift.
        // Policy: HR Policy §8.5 — Long Service Recognition.
        double loyaltyUplift = yearsServed >= 5 ? 0.02 : 0.0;

        return salary * (baseMultiplier + loyaltyUplift);
    }
}
```

---

## Exercises

1. Take the code below and add only the comments that are genuinely useful. Remove any that just restate the code:
   ```java
   int x = 10; // set x to 10
   int y = 20; // set y to 20
   // add x and y
   int sum = x + y;
   // print the sum
   System.out.println(sum); // print
   ```

2. Write a Javadoc comment for this method:
   ```java
   public int findMax(int[] numbers) {
       int max = numbers[0];
       for (int n : numbers) {
           if (n > max) max = n;
       }
       return max;
   }
   ```

3. Write a method `convertCelsiusToFahrenheit(double celsius)` with a full Javadoc comment including `@param`, `@return`, and a brief note about the formula's source.

4. Find three examples of bad comments in this snippet and explain why each is bad:
   ```java
   // 2025-01-10: added by John
   // i is the loop counter
   for (int i = 0; i < list.size(); i++) {
       // get item at index i
       String item = list.get(i);
       // print item
       System.out.println(item);
   }
   // ==================== END OF METHOD ====================
   ```

---

## Key Takeaways

- Three comment types: `//` (single-line), `/* */` (multi-line), `/** */` (Javadoc)
- Comment the **why**, not the **what** (Amalitech §10.1)
- No journal comments — use Git history instead (§10.2)
- No positional markers (§10.3)
- Never commit commented-out code — delete it, trust Git
- Write Javadoc for every public class and method

---

## Related Topics

- [Debugging](../debugging/README.md) — using `System.out.println` for temporary debug output
- [Amalitech Coding Standards §10](../../../amalitech-coding-standards/README.md)
