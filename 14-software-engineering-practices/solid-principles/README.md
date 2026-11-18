# The SOLID Principles

> 🏢 **Official Amalitech Standard — §12, Amalitech Coding Standards Java Edition**
>
> The examples in this document are reproduced directly from the AmaliTech Coding Standards Java Edition (2026). They represent the official standard that AmaliTech engineers are required to follow. Where you see this badge, the example is from the official document.

SOLID is an acronym for five design principles introduced by Robert C. Martin. They lead to software that is easier to maintain and extend as a project grows, and help avoid code smells. AmaliTech strongly encourages its engineers to follow them.

- **S** — Single-responsibility principle
- **O** — Open-closed principle
- **L** — Liskov substitution principle
- **I** — Interface segregation principle
- **D** — Dependency inversion principle

---

## 12.1 — Single-Responsibility Principle (SRP)

> **A class should have one, and only one, reason to change: it should have one job.**

### Explanation

If a class has multiple responsibilities, a change to one responsibility can inadvertently affect the others. It also becomes harder to test: you cannot test one piece of behavior without the others.

A common sign of SRP violation: the class name contains "And" (`UserAuthAndSettings`) or uses a vague word like "Manager", "Handler", or "Helper" that could mean anything.

### 🏢 Official Amalitech Example — BAD

```java
class UserSettings {
    private final User user;

    UserSettings(User user) {
        this.user = user;
    }

    void changeSettings(Settings settings) {
        if (verifyCredentials()) {
            user.apply(settings);
        }
    }

    private boolean verifyCredentials() {
        return user.hasValidSession() && !user.isLocked();
    }
}
```

**What's wrong:** `UserSettings` has two reasons to change — a change to how settings are applied, and a change to how credentials are verified. Authentication logic does not belong here.

### 🏢 Official Amalitech Example — GOOD

```java
class UserAuth {
    boolean verifyCredentials(User user) {
        return user.hasValidSession() && !user.isLocked();
    }
}

class UserSettings {
    private final User user;
    private final UserAuth auth;

    UserSettings(User user, UserAuth auth) {
        this.user = user;
        this.auth = auth;
    }

    void changeSettings(Settings settings) {
        if (auth.verifyCredentials(user)) {
            user.apply(settings);
        }
    }
}
```

**Why it's better:** `UserAuth` has one job: credential verification. `UserSettings` has one job: managing settings. Each can change independently.

### Practical Exercise

You have this class:

```java
class OrderProcessor {
    void processOrder(Order order) {
        // validate the order items
        for (OrderItem item : order.getItems()) {
            if (item.getQuantity() <= 0) {
                throw new IllegalArgumentException("Invalid quantity");
            }
        }

        // calculate total price
        double total = order.getItems().stream()
            .mapToDouble(item -> item.getPrice() * item.getQuantity())
            .sum();
        order.setTotal(total);

        // save to database
        database.save(order);

        // send confirmation email
        emailService.sendConfirmation(order.getCustomerEmail(), total);
    }
}
```

**Task:** Identify how many responsibilities this class has. Extract each responsibility into its own class. The `processOrder` method should orchestrate them.

---

## 12.2 — Open-Closed Principle (OCP)

> **Software entities (classes, modules, methods) should be open for extension but closed for modification: you can add new behaviour without changing code that already works.**

### Explanation

When adding a new feature requires editing an existing, tested class, you risk breaking what already works. OCP says: design your classes so that adding new behavior means adding new code (a new class, a new implementation), not modifying existing code.

The typical solution is to introduce an abstraction (interface or abstract class) that existing code depends on, then add new behavior by writing a new implementation.

### 🏢 Official Amalitech Example — BAD

```java
class RegistrationValidator {
    List<String> validate(Registration registration) {
        List<String> problems = new ArrayList<>();
        if (registration.email() == null || !registration.email().contains("@")) {
            problems.add("A valid email address is required");
        }
        if (registration.age() < 16) {
            problems.add("Members must be at least 16");
        }
        // every new rule means editing this method
        return problems;
    }
}
```

**What's wrong:** Every new validation rule requires editing `RegistrationValidator.validate()`. If you change a rule and introduce a bug, all rules are affected.

### 🏢 Official Amalitech Example — GOOD

```java
interface RegistrationRule {
    Optional<String> check(Registration registration);
}

class RegistrationValidator {
    private final List<RegistrationRule> rules;

    RegistrationValidator(List<RegistrationRule> rules) {
        this.rules = List.copyOf(rules);
    }

    List<String> validate(Registration registration) {
        return rules.stream()
                .map(rule -> rule.check(registration))
                .flatMap(Optional::stream)
                .toList();
    }
}

class MinimumAgeRule implements RegistrationRule {
    private static final int MINIMUM_AGE = 16;

    public Optional<String> check(Registration registration) {
        return registration.age() < MINIMUM_AGE
                ? Optional.of("Members must be at least " + MINIMUM_AGE)
                : Optional.empty();
    }
}
```

**Why it's better:** A new rule is a new class that implements `RegistrationRule`. `RegistrationValidator` never changes. The validator is closed for modification and open for extension.

### Practical Exercise

You have this price calculator:

```java
class PriceCalculator {
    double calculate(Product product, String customerType) {
        if (customerType.equals("REGULAR")) {
            return product.getBasePrice();
        } else if (customerType.equals("PREMIUM")) {
            return product.getBasePrice() * 0.9;
        } else if (customerType.equals("EMPLOYEE")) {
            return product.getBasePrice() * 0.7;
        }
        return product.getBasePrice();
    }
}
```

**Task:** Apply OCP. Create a `PricingStrategy` interface and separate implementations for each customer type. The calculator should work with any strategy without modification when a new type is added.

---

## 12.3 — Liskov Substitution Principle (LSP)

> **Every subclass must be usable wherever its parent class is expected, without the caller noticing a difference.**

### Explanation

LSP states that if `S` extends `T`, then objects of type `S` can replace objects of type `T` without breaking correctness. In other words: a subclass must honor the contract of its parent class — same method signatures, same preconditions, same postconditions.

A common violation: a subclass overrides a method and throws `UnsupportedOperationException`. Any caller relying on the parent contract is broken.

### 🏢 Official Amalitech Example — BAD

```java
class Rectangle {
    protected int width;
    protected int height;

    void setWidth(int width) { this.width = width; }
    void setHeight(int height) { this.height = height; }
    int area() { return width * height; }
}

class Square extends Rectangle {
    @Override void setWidth(int width) { this.width = width; this.height = width; }
    @Override void setHeight(int height) { this.width = height; this.height = height; }
}

class Renderer {
    int areaOfFourByFive(Rectangle rectangle) {
        rectangle.setWidth(4);
        rectangle.setHeight(5);
        return rectangle.area(); // 25 for a Square, not 20 — LSP violation
    }
}
```

**What's wrong:** `Square` violates the contract of `Rectangle`. Setting width and height independently is a reasonable expectation for a rectangle; a square breaks it. Code written for `Rectangle` produces wrong results with `Square`.

### 🏢 Official Amalitech Example — GOOD

```java
sealed interface Shape permits Rectangle, Square {
    int area();
}

record Rectangle(int width, int height) implements Shape {
    public int area() { return width * height; }
}

record Square(int side) implements Shape {
    public int area() { return side * side; }
}
```

**Why it's better:** `Square` no longer pretends to be a `Rectangle` with different rules. Both are `Shape`s. Any code that works with a `Shape` works correctly with either. Neither can break the other's contract.

> *Note: This example puts several short members on one line to fit the page; in production code, follow §7 and §11.1.* — Amalitech Coding Standards

### Practical Exercise

You have this hierarchy:

```java
class Bird {
    void fly() {
        System.out.println("Flying...");
    }
}

class Penguin extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException("Penguins cannot fly");
    }
}
```

**Task:** Identify the LSP violation. Redesign the hierarchy using an interface. Hint: not all birds fly — what behaviors do all birds share?

---

## 12.4 — Interface Segregation Principle (ISP)

> **A client should never be forced to implement an interface it does not use, or to depend on methods it does not need.**

### Explanation

Fat interfaces (interfaces with many methods) force implementing classes to provide methods they do not support. This leads to `throw new UnsupportedOperationException()` bodies, which violate LSP and mislead callers.

The fix is to break large interfaces into smaller, focused ones. A class can implement multiple interfaces; it cannot choose which methods of a single interface to implement.

### 🏢 Official Amalitech Example — BAD

```java
interface Device {
    void print(Document document);
    void scan(Document document);
    void fax(Document document);
}

class BasicPrinter implements Device {
    public void print(Document document) { /* prints */ }
    public void scan(Document document) { throw new UnsupportedOperationException(); }
    public void fax(Document document) { throw new UnsupportedOperationException(); }
}
```

**What's wrong:** `BasicPrinter` is forced to implement `scan` and `fax` even though it cannot do them. Any code that calls `basicPrinter.scan(doc)` will fail at runtime.

### 🏢 Official Amalitech Example — GOOD

```java
interface Printer {
    void print(Document document);
}

interface Scanner {
    void scan(Document document);
}

class BasicPrinter implements Printer {
    public void print(Document document) { /* prints */ }
}

class OfficeMachine implements Printer, Scanner {
    public void print(Document document) { /* prints */ }
    public void scan(Document document) { /* scans */ }
}
```

**Why it's better:** `BasicPrinter` only implements what it supports. `OfficeMachine` implements both. Code that needs printing depends on `Printer`; code that needs scanning depends on `Scanner`. Neither depends on the other.

### Practical Exercise

You have this interface:

```java
interface Animal {
    void eat();
    void sleep();
    void swim();
    void fly();
    void run();
}
```

**Task:** Different animals support different behaviors. A fish swims but does not fly. A bird flies but does not swim (mostly). Segregate this interface into focused interfaces and show how `Fish`, `Bird`, and `Dog` would implement them.

---

## 12.5 — Dependency Inversion Principle (DIP)

> **Depend on abstractions, not on concrete classes. High-level modules should not depend on low-level modules; both should depend on abstractions.**

### Explanation

When a high-level class (business logic) directly creates or references a low-level class (database, HTTP client, email service), it is tightly coupled. Changing the low-level component requires changing the high-level component. Testing the high-level component requires the actual low-level component.

The solution: introduce an interface between them. The high-level module depends on the interface. The concrete implementation is injected (passed in), not created internally. This is the basis of dependency injection in Spring Boot.

### 🏢 Official Amalitech Example — BAD

```java
class InventoryTracker {
    // hard-wired to one implementation
    private final HttpInventoryRequester requester = new HttpInventoryRequester();

    void requestItems(List<String> items) {
        items.forEach(requester::requestItem);
    }
}
```

**What's wrong:** `InventoryTracker` creates its own `HttpInventoryRequester`. You cannot:
- Test `InventoryTracker` without making real HTTP calls
- Use a different requester (WebSocket, message queue) without editing `InventoryTracker`

### 🏢 Official Amalitech Example — GOOD

```java
interface InventoryRequester {
    void requestItem(String item);
}

class InventoryTracker {
    private final InventoryRequester requester;

    InventoryTracker(InventoryRequester requester) {
        this.requester = requester;
    }

    void requestItems(List<String> items) {
        items.forEach(requester::requestItem);
    }
}

// The caller chooses the implementation:
// new InventoryTracker(new WebSocketInventoryRequester())
```

**Why it's better:** `InventoryTracker` depends on the `InventoryRequester` interface, not an implementation. In production, inject `HttpInventoryRequester`. In a test, inject a fake. The tracker never changes when the requester changes.

This is exactly how Spring Boot works: `@Service` classes inject `@Repository` interfaces through their constructors. Spring provides the implementation.

### Practical Exercise

You have this notification service:

```java
class OrderService {
    private final EmailNotifier notifier = new EmailNotifier();

    void placeOrder(Order order) {
        // process the order...
        notifier.sendOrderConfirmation(order.getCustomerEmail(), order.getId());
    }
}
```

**Task:**  
1. Create a `NotificationService` interface with a `sendOrderConfirmation(String email, Long orderId)` method.
2. Create `EmailNotificationService` and `SmsNotificationService` implementing the interface.
3. Refactor `OrderService` to depend on `NotificationService` through constructor injection.
4. Show how to use `OrderService` with each notification type, and how to test it with a mock.

---

## Applying SOLID Together

The five principles work together. In a typical Spring Boot layered architecture:

```
@RestController (depends on interface via constructor) ← DIP
    ↓ calls
@Service (one job: business logic) ← SRP
    ↓ calls
Repository interface ← ISP (only the methods needed)
    ↓ implemented by
JpaRepository implementation ← OCP (new queries = new method, not editing existing)
    ↓ operates on
@Entity ← LSP (subtypes honor contracts)
```

| Principle | Spring Boot manifestation |
|-----------|--------------------------|
| SRP | Controller / Service / Repository are separate classes |
| OCP | Add a new repository query method; never edit `JpaRepository` |
| LSP | Custom exceptions extend the right exception type |
| ISP | Define small repository interfaces; don't expose all JPA methods everywhere |
| DIP | Constructor injection throughout; no `new ConcreteService()` in controllers |
