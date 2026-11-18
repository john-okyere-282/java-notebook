# Interfaces

## What is an Interface?
An interface defines a **contract** — a set of methods that any implementing class agrees to provide. An interface says **what** a class can do, not **how** it does it.

**Analogy:** A USB port is an interface. Any device that follows the USB standard can plug in, regardless of brand, internal circuitry, or purpose. The USB spec defines the contract (connector shape, voltage, data protocol); the device fulfils it.

---

## Declaring and Implementing an Interface

```java
// Interface declaration
public interface Printable {
    void print();  // abstract by default — no body, no 'abstract' keyword needed
}

public interface Scannable {
    void scan();
}

// A class can implement MULTIPLE interfaces
public class OfficePrinter implements Printable, Scannable {

    @Override
    public void print() {
        System.out.println("Printing document...");
    }

    @Override
    public void scan() {
        System.out.println("Scanning document...");
    }
}
```

```java
OfficePrinter printer = new OfficePrinter();
printer.print();   // Printing document...
printer.scan();    // Scanning document...

// Polymorphism through interfaces
Printable p = new OfficePrinter();
p.print();         // works — OfficePrinter IS-A Printable
```

---

## Default Methods (Java 8+)

Interfaces can have methods with a default implementation. Classes can use the default or override it.

```java
public interface Greeting {
    String getName();     // abstract — must be implemented

    default void greet() {           // default — has implementation
        System.out.println("Hello, " + getName() + "!");
    }

    default void greetFormally() {
        System.out.println("Good day, " + getName() + ". How do you do?");
    }
}

public class FormalPerson implements Greeting {
    private String name;

    public FormalPerson(String name) { this.name = name; }

    @Override
    public String getName() { return name; }

    @Override
    public void greet() {
        // override the default — use formal greeting instead
        greetFormally();
    }
}

public class CasualPerson implements Greeting {
    private String name;

    public CasualPerson(String name) { this.name = name; }

    @Override
    public String getName() { return name; }
    // greet() not overridden — uses the default "Hello, ...!"
}
```

---

## Static Methods in Interfaces (Java 8+)

Interfaces can have static methods — utility methods related to the interface:

```java
public interface Validator {
    boolean isValid(String input);

    static Validator notEmpty() {
        return input -> input != null && !input.isBlank();
    }
}
```

---

## Functional Interfaces

An interface with exactly **one abstract method** is a functional interface. It can be used with **lambda expressions** (covered in Phase 07):

```java
@FunctionalInterface
public interface Calculator {
    int calculate(int a, int b);
}

// Using a lambda instead of an anonymous class
Calculator add      = (a, b) -> a + b;
Calculator multiply = (a, b) -> a * b;

System.out.println(add.calculate(5, 3));       // 8
System.out.println(multiply.calculate(5, 3));  // 15
```

The `@FunctionalInterface` annotation is optional but recommended — the compiler enforces the one-method rule.

---

## Interfaces vs Abstract Classes (Quick Reference)

| | Interface | Abstract Class |
|---|---|---|
| Constructor | ❌ No | ✅ Yes |
| Instance fields | ❌ No | ✅ Yes |
| Concrete methods | ✅ default methods | ✅ Yes |
| Multiple | ✅ A class can implement many | ❌ One parent only |
| Use when | Defining a capability/contract | Sharing code + is-a relationship |

---

## SOLID Principles in Interfaces

### Interface Segregation Principle (Amalitech §12.4)

> 📖 **Official Amalitech Standard:** §12.4 — "A client should never be forced to implement an interface it does not use."

**BAD — one large interface:**
```java
public interface Worker {
    void work();
    void eat();
    void sleep();
    void attendMeeting();
}
// A Robot worker can work but can't eat or sleep — forced to implement empty methods
```

**GOOD — small, focused interfaces:**
```java
public interface Workable { void work(); }
public interface Eatable  { void eat(); }
public interface Sleepable { void sleep(); }

public class Human implements Workable, Eatable, Sleepable { ... }
public class Robot implements Workable { ... }  // only what makes sense
```

### Dependency Inversion Principle (Amalitech §12.5)

> 📖 **Official Amalitech Standard:** §12.5 — "Depend on abstractions, not on concrete classes."

```java
// BAD — depends on a concrete class
public class ReportService {
    private MySQLDatabase database;  // tightly coupled — cannot swap
    // ...
}

// GOOD — depends on an interface
public class ReportService {
    private Database database;       // loosely coupled — can inject any Database
    public ReportService(Database database) {
        this.database = database;
    }
}
```

This makes `ReportService` testable (you can inject a fake database) and flexible (you can swap MySQL for PostgreSQL without changing `ReportService`).

---

## Exercises

**Exercise 1:** Create a `Sortable` interface with a method `sortKey()` that returns an `int`. Create three classes: `Student` (sortKey = gpa × 100), `Product` (sortKey = price × 100), `Movie` (sortKey = rating × 10). Sort a mixed list by their sort keys.

**Exercise 2:** Create interfaces `Flyable` (with `fly()`) and `Swimmable` (with `swim()`). Create classes: `Duck` (implements both), `Fish` (Swimmable only), `Eagle` (Flyable only). Demonstrate polymorphism through each interface.

**Exercise 3:** Create a `Validator<T>` functional interface with method `boolean validate(T value)`. Create three validators using lambdas: one that checks a String is not empty, one that checks an int is positive, one that checks a String is a valid email (contains `@`).

**Exercise 4:** Demonstrate the Interface Segregation Principle: take a large `Printer` interface that has `print()`, `scan()`, `fax()`, and `copy()`. Split it into four small interfaces. Create a `BasicPrinter` that implements only `print()` and a `MultiFunctionPrinter` that implements all four.

**Exercise 5:** Create a `Comparable`-like interface `Rankable` with method `int rank()`. Implement it in `Employee` (rank = salary / 1000), `Student` (rank = gpa × 100). Write a generic method `findHighest(List<Rankable>)` that returns the item with the highest rank.
