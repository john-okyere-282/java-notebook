# Composition vs Inheritance

## Has-a vs Is-a

The key question when designing class relationships:

- **Is-a relationship → Inheritance:** A `Car` IS-A `Vehicle`. A `Dog` IS-A `Animal`.
- **Has-a relationship → Composition:** A `Car` HAS-A `Engine`. A `House` HAS-A `Door`.

Inheritance often feels like the natural choice because "reusing code is good." But inheritance creates tight coupling that can make code brittle and hard to change.

---

## The Problem with Deep Inheritance

```java
// BAD: inheritance used to share code without a true is-a relationship
class Animal       { void breathe() { System.out.println("breathing"); } }
class LandAnimal   extends Animal  { void walk()       { System.out.println("walking"); } }
class Pet          extends LandAnimal { void beFriendly() { System.out.println("friendly"); } }
class Dog          extends Pet     { void fetch()      { System.out.println("fetching"); } }
```

**Problems:**
- What about a `Dolphin`? It's friendly and an animal, but NOT a land animal.
- Changing `LandAnimal` could break `Dog` in unexpected ways.
- The hierarchy is brittle — real-world classifications don't map cleanly onto a single tree.

---

## Composition — The Better Approach

Break each responsibility into its own class. Then **compose** them:

```java
// Each responsibility is its own class
public class Engine {
    private int horsepower;

    public Engine(int horsepower) {
        this.horsepower = horsepower;
    }

    public void start() {
        System.out.println("Engine (" + horsepower + "hp) started.");
    }

    public void stop() {
        System.out.println("Engine stopped.");
    }
}

public class GPS {
    public String getLocation() {
        return "52.3676° N, 4.9041° E";   // Amsterdam
    }
}

public class Car {
    private String brand;
    private Engine engine;    // Car HAS-A Engine
    private GPS gps;          // Car HAS-A GPS

    public Car(String brand, Engine engine, GPS gps) {
        this.brand = brand;
        this.engine = engine;
        this.gps = gps;
    }

    public void start() {
        engine.start();       // delegate to Engine
        System.out.println(brand + " is ready to drive.");
    }

    public void navigate() {
        System.out.println("Navigating to: " + gps.getLocation());
    }
}
```

```java
Engine v8   = new Engine(400);
GPS    satnav = new GPS();
Car    car  = new Car("Ford Mustang", v8, satnav);

car.start();      // Engine (400hp) started. / Ford Mustang is ready to drive.
car.navigate();   // Navigating to: 52.3676° N, 4.9041° E
```

**Key benefit:** You can swap any component independently. Need a diesel engine instead of petrol? Just pass a different `Engine` object. The `Car` class doesn't change at all.

---

## Amalitech §2.2 — Composition over Inheritance

> 📖 **Official Amalitech Standard:** §2.2 — "Composition over inheritance: design types around what they do, not what they are. Prefer holding a collaborator to extending a class."

This principle is so important it has its own section in the standards because beginners (and experienced developers!) reach for inheritance when composition is the better choice.

---

## Comparison

| | Inheritance | Composition |
|---|---|---|
| Relationship | Is-a | Has-a |
| Coupling | Tight (child depends on parent internals) | Loose (components are independent) |
| Flexibility | Low (hierarchy is fixed) | High (swap components at runtime) |
| Testability | Harder (must test with parent) | Easier (test each component separately) |
| When to use | True is-a + stable hierarchy | Has-a + want flexibility |

---

## When to Use Each

**Use inheritance when:**
- The relationship is a genuine, stable is-a: `Cat` IS-A `Animal`
- The child meaningfully extends and specialises the parent
- The hierarchy is unlikely to need restructuring

**Use composition when:**
- The relationship is has-a: `Car` HAS-A `Engine`
- You want to swap or reuse behaviour independently
- The alternative is a deep or awkward inheritance hierarchy
- You are mixing capabilities from multiple sources

---

## Composition with Interfaces — Strategy Pattern

One powerful use: hold an interface reference and swap the implementation at runtime.

```java
public interface PaymentStrategy {
    void pay(double amount);
}

public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " with credit card.");
    }
}

public class PayPalPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " via PayPal.");
    }
}

public class ShoppingCart {
    private PaymentStrategy paymentStrategy;   // HAS-A PaymentStrategy

    public ShoppingCart(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }

    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;  // swap at runtime!
    }

    public void checkout(double total) {
        paymentStrategy.pay(total);
    }
}
```

```java
ShoppingCart cart = new ShoppingCart(new CreditCardPayment());
cart.checkout(49.99);   // Paid $49.99 with credit card.

cart.setPaymentStrategy(new PayPalPayment());
cart.checkout(99.99);   // Paid $99.99 via PayPal.
```

No inheritance needed. Adding a `BitcoinPayment` only requires a new class — `ShoppingCart` never changes.

---

## Exercises

**Exercise 1:** Create a `Logger` class with a method `log(String message)`. Create a `UserService` class that uses composition to hold a `Logger` instance. `UserService` has methods `createUser(String name)` and `deleteUser(String name)` — both should log their actions.

**Exercise 2:** Create a `Sorter` interface with method `sort(int[] array)`. Implement `BubbleSorter` and `SelectionSorter`. Create a `DataProcessor` class that holds a `Sorter` and can swap sorting algorithms at runtime. Test with both.

**Exercise 3:** Refactor this inheritance-based design to use composition instead:
```java
class Animal    { void breathe() {} }
class FlyingAnimal extends Animal { void fly() {} }
class SwimmingAnimal extends Animal { void swim() {} }
// Problem: what about a duck that both flies AND swims?
```

**Exercise 4:** Create a `Notification` system using composition. A `NotificationService` has-a list of `NotificationChannel` objects (interface with `send(String message)`). Implement `EmailChannel`, `SMSChannel`, and `PushChannel`. `NotificationService.broadcast(String)` sends through all channels.

**Exercise 5:** Explain the difference between these two designs in a comment, and state which is better for this case:
```java
// Design A: inheritance
class LoggingUserService extends UserService { ... }

// Design B: composition (decorator)
class LoggingUserService implements UserService {
    private UserService delegate;
    private Logger logger;
    // ...
}
```
