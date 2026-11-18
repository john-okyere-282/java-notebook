# JUnit 5

## Architecture

JUnit 5 is not a single JAR — it is composed of three sub-projects:

| Sub-project | Purpose |
|-------------|---------|
| **JUnit Platform** | Foundation for running test frameworks on the JVM. Used by IDEs and build tools. |
| **JUnit Jupiter** | The new programming model and extension model for writing tests (this is what you use). |
| **JUnit Vintage** | Runs JUnit 3 and JUnit 4 tests on the JUnit 5 platform (backward compatibility). |

The dependency `junit-jupiter` pulls in all of Jupiter.

---

## Maven Dependency

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.1</version>
    <scope>test</scope>
</dependency>
```

Also add the Surefire plugin (version 3.x) to your `pom.xml` — otherwise Maven won't discover JUnit 5 tests.

---

## Basic Test Structure

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    @Test
    void should_returnSum_when_addingTwoPositiveNumbers() {
        Calculator calc = new Calculator();
        int result = calc.add(3, 7);
        assertEquals(10, result);
    }
}
```

Test classes and methods do **not** need to be `public` in JUnit 5 — package-private is fine.

---

## Lifecycle Annotations

```java
import org.junit.jupiter.api.*;

class OrderServiceTest {

    @BeforeAll
    static void setupOnce() {
        // Runs once before all tests in this class — must be static
        System.out.println("Starting test suite");
    }

    @AfterAll
    static void teardownOnce() {
        // Runs once after all tests — must be static
        System.out.println("Test suite complete");
    }

    @BeforeEach
    void setup() {
        // Runs before every single test — good for creating fresh test objects
    }

    @AfterEach
    void teardown() {
        // Runs after every single test — good for cleanup
    }

    @Test
    void someTest() { }
}
```

---

## @DisplayName — Readable Test Names

```java
import org.junit.jupiter.api.DisplayName;

@DisplayName("Order Service")
class OrderServiceTest {

    @Test
    @DisplayName("should throw InsufficientFundsException when balance is too low")
    void insufficient_funds() {
        // ...
    }
}
```

`@DisplayName` appears in the IDE test report and CI output. Use natural language — it makes failures immediately understandable.

---

## Assertions

```java
import static org.junit.jupiter.api.Assertions.*;

// Equality
assertEquals(expected, actual);
assertEquals(expected, actual, "failure message shown if assertion fails");
assertNotEquals(unexpected, actual);

// Boolean
assertTrue(condition);
assertFalse(condition);

// Null checks
assertNull(object);
assertNotNull(object);

// Same reference
assertSame(expected, actual);

// Exception testing
assertThrows(IllegalArgumentException.class, () -> service.method(badInput));

// All assertions must pass — reports all failures, not just the first
assertAll(
    () -> assertEquals("Alice", user.getName()),
    () -> assertEquals(30, user.getAge()),
    () -> assertNotNull(user.getEmail())
);

// Fail explicitly
fail("This code should not be reached");
```

### assertThrows — Verify exception message

```java
IllegalArgumentException ex = assertThrows(
    IllegalArgumentException.class,
    () -> new BankAccount(-100)
);
assertEquals("Initial balance cannot be negative", ex.getMessage());
```

---

## @Nested — Organising Related Tests

```java
@DisplayName("BankAccount")
class BankAccountTest {

    @Nested
    @DisplayName("deposit()")
    class Deposit {

        @Test
        @DisplayName("increases balance by the deposited amount")
        void increases_balance() { }

        @Test
        @DisplayName("throws IllegalArgumentException for negative amount")
        void rejects_negative_amount() { }
    }

    @Nested
    @DisplayName("withdraw()")
    class Withdraw {

        @Test
        @DisplayName("decreases balance by the withdrawn amount")
        void decreases_balance() { }

        @Test
        @DisplayName("throws InsufficientFundsException when balance is too low")
        void rejects_overdraft() { }
    }
}
```

`@Nested` groups tests visually in reports. Each nested class gets its own `@BeforeEach`/`@AfterEach` lifecycle.

---

## @Disabled

```java
@Test
@Disabled("Temporarily disabled — waiting for external API key in CI environment")
void should_connectToPaymentGateway() {
    // ...
}
```

Always provide a reason. A disabled test with no reason is a maintenance hazard.

---

## Test Naming Conventions

Two conventions are common. Pick one and apply it consistently across the codebase.

```java
// Convention 1: should_<expectedBehaviour>_when_<condition>
void should_returnNull_when_userNotFound() { }
void should_throwException_when_passwordTooShort() { }

// Convention 2: given_<precondition>_when_<action>_then_<result>
void given_emptyCart_when_checkout_then_throwException() { }
void given_validUser_when_login_then_returnToken() { }
```

---

## Running Tests in IntelliJ IDEA

- **Single test:** Click the green ▶ gutter icon next to the method.
- **Entire class:** Click the green ▶ gutter icon next to the class name.
- **All tests:** Right-click the `test/java` folder → "Run All Tests".
- **From terminal:** `mvn test`

Failed tests appear in red with the actual vs expected values. Click a failure to jump to the assertion.

---

## Complete Example — Testing a Calculator

**Calculator.java** (in `src/main/java`):

```java
package com.amalitech.calculator;

public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public double divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return (double) a / b;
    }
}
```

**CalculatorTest.java** (in `src/test/java`):

```java
package com.amalitech.calculator;

import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

@DisplayName("Calculator")
class CalculatorTest {

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    @Nested
    @DisplayName("add()")
    class Add {

        @Test
        @DisplayName("returns sum of two positive integers")
        void should_returnSum_when_addingPositives() {
            assertEquals(10, calculator.add(3, 7));
        }

        @Test
        @DisplayName("handles negative numbers correctly")
        void should_returnSum_when_addingNegatives() {
            assertEquals(-5, calculator.add(-3, -2));
        }

        @Test
        @DisplayName("returns the other number when adding zero")
        void should_returnSameNumber_when_addingZero() {
            assertEquals(5, calculator.add(5, 0));
        }
    }

    @Nested
    @DisplayName("divide()")
    class Divide {

        @Test
        @DisplayName("returns correct quotient")
        void should_returnQuotient_when_dividingNonZero() {
            assertEquals(2.5, calculator.divide(5, 2));
        }

        @Test
        @DisplayName("throws ArithmeticException when dividing by zero")
        void should_throwException_when_divisorIsZero() {
            ArithmeticException ex = assertThrows(
                ArithmeticException.class,
                () -> calculator.divide(10, 0)
            );
            assertEquals("Cannot divide by zero", ex.getMessage());
        }
    }
}
```
