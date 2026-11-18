# Test-Driven Development (TDD)

## What Is TDD?

Test-Driven Development is a development process where you write a failing test before writing the code that makes it pass. It is not primarily a testing technique — it is a design technique. Writing the test first forces you to think about the interface before the implementation.

---

## The Red-Green-Refactor Cycle

```
  ┌─────────────────────────────────────────────┐
  │                                             │
  │   ┌──────────┐    write     ┌──────────┐   │
  │   │          │   failing    │          │   │
  │   │ REFACTOR │◄─────────────│   RED    │   │
  │   │          │              │          │   │
  │   └──────────┘              └──────────┘   │
  │         │                        │         │
  │  clean  │                        │ write   │
  │  code   │                        │ minimal │
  │  up     │                        │ code    │
  │         ▼                        ▼         │
  │   ┌──────────┐              ┌──────────┐   │
  │   │          │   test now   │          │   │
  │   │  GREEN   │◄─────────────│  GREEN   │   │
  │   │          │   passes     │          │   │
  │   └──────────┘              └──────────┘   │
  │                                             │
  └─────────────────────────────────────────────┘
```

1. **Red** — Write a small test for a feature that does not exist yet. Run it; it must fail (red). If it passes without any implementation, the test is testing nothing.
2. **Green** — Write the minimal code needed to make the test pass. Do not over-engineer at this step.
3. **Refactor** — Improve the code (remove duplication, clarify names, simplify logic) while keeping all tests green. The tests are your safety net.

Repeat this cycle in small, deliberate steps.

---

## Why TDD Improves Design

- **Forces small, focused classes.** When you write tests first, deeply coupled classes are painful to test, so you naturally reduce coupling.
- **Prevents gold-plating.** You only write code for a test that exists — no speculative features.
- **Creates regression coverage automatically.** Every feature has a test by definition.
- **Makes requirements explicit.** A test is a precise, executable statement of a requirement.

---

## The Arrange-Act-Assert Pattern

Every test body should follow three phases, in order:

```java
@Test
void should_returnDiscountedPrice_when_couponIsValid() {
    // Arrange — set up the objects and inputs
    PricingService pricingService = new PricingService();
    double originalPrice = 100.0;
    String couponCode    = "SAVE20";

    // Act — perform the single action being tested
    double discountedPrice = pricingService.applyDiscount(originalPrice, couponCode);

    // Assert — verify the outcome
    assertEquals(80.0, discountedPrice, 0.001);
}
```

Separate the three sections visually (blank line or comment). Never combine the Act and Assert step into one line — it makes the test harder to debug when it fails.

---

## TDD Walkthrough — Building a ShoppingCart

### Step 1 — Write the first failing test

```java
class ShoppingCartTest {

    @Test
    void should_returnZero_when_cartIsEmpty() {
        ShoppingCart cart = new ShoppingCart();
        assertEquals(0.0, cart.getTotal());
    }
}
```

Run → **RED**: `ShoppingCart` does not exist.

### Step 2 — Write minimal code to make it pass

```java
public class ShoppingCart {
    public double getTotal() {
        return 0.0;
    }
}
```

Run → **GREEN**.

### Step 3 — Write the next test (adding an item)

```java
@Test
void should_returnItemPrice_when_oneItemAdded() {
    ShoppingCart cart = new ShoppingCart();
    cart.addItem("Apple", 1.50);
    assertEquals(1.50, cart.getTotal(), 0.001);
}
```

Run → **RED**: `addItem()` does not exist.

### Step 4 — Make it pass

```java
import java.util.ArrayList;
import java.util.List;

public class ShoppingCart {
    private final List<Double> prices = new ArrayList<>();

    public void addItem(String name, double price) {
        prices.add(price);
    }

    public double getTotal() {
        return prices.stream().mapToDouble(Double::doubleValue).sum();
    }
}
```

Run → **GREEN**.

### Step 5 — Write more tests (multiple items, edge cases)

```java
@Test
void should_returnSumOfPrices_when_multipleItemsAdded() {
    ShoppingCart cart = new ShoppingCart();
    cart.addItem("Apple", 1.50);
    cart.addItem("Bread", 2.00);
    cart.addItem("Milk", 1.25);
    assertEquals(4.75, cart.getTotal(), 0.001);
}

@Test
void should_throwException_when_negativePrice() {
    ShoppingCart cart = new ShoppingCart();
    assertThrows(
        IllegalArgumentException.class,
        () -> cart.addItem("Apple", -1.00)
    );
}

@Test
void should_returnZero_when_cartIsCleared() {
    ShoppingCart cart = new ShoppingCart();
    cart.addItem("Apple", 1.50);
    cart.clear();
    assertEquals(0.0, cart.getTotal());
}
```

### Step 6 — Make each test pass, then refactor

```java
public class ShoppingCart {
    private final List<Double> prices = new ArrayList<>();

    public void addItem(String name, double price) {
        if (price < 0) {
            throw new IllegalArgumentException("Price cannot be negative: " + price);
        }
        prices.add(price);
    }

    public void clear() {
        prices.clear();
    }

    public double getTotal() {
        return prices.stream().mapToDouble(Double::doubleValue).sum();
    }
}
```

---

## AmaliTech §9 Requirements in Practice

| Requirement | How to implement |
|-------------|-----------------|
| 100% meaningful coverage | Write a test for every code path, including error paths |
| Single concept per test | One `@Test` method = one behaviour; one `assert` per test where possible |
| Clean and readable tests | Follow naming conventions; use `@DisplayName`; use `@Nested` |
| Meaningful assertions | Add a failure message to assertions; test the exact exception message |

---

## Mutation Testing with PIT

Coverage alone does not guarantee good tests. You can have 100% line coverage with assertions that always pass. **Mutation testing** verifies your assertions.

PIT (Pitest) automatically modifies (mutates) your production code — changing `>` to `>=`, deleting a line, etc. — and checks that your tests fail for each mutation. If a mutation survives (all tests still pass), your test suite has a gap.

Add PIT to your Maven build:

```xml
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>1.15.3</version>
    <dependencies>
        <dependency>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-junit5-plugin</artifactId>
            <version>1.2.1</version>
        </dependency>
    </dependencies>
</plugin>
```

Run:

```bash
mvn org.pitest:pitest-maven:mutationCoverage
```

The report appears in `target/pit-reports/`. Aim for a mutation score above 80%.
