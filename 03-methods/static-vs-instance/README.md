# Static vs Instance Methods

## Overview
Java methods come in two kinds:
- **Static methods** belong to the class itself.
- **Instance methods** belong to individual objects of the class.

The difference matters for how you call them, what data they can access, and whether they can be overridden.

---

## Static Methods

A static method belongs to the **class**, not to any particular object. You call it on the class name, not on an instance. It has no access to instance fields.

```java
public class MathUtils {

    public static int square(int n) {
        return n * n;
    }

    public static boolean isEven(int n) {
        return n % 2 == 0;
    }
}

// Called on the CLASS name — no object needed
System.out.println(MathUtils.square(5));    // 25
System.out.println(MathUtils.isEven(4));    // true
```

You have been writing `public static` methods in `main()` and your helper methods all along — they are static.

**When to use static:**
- Pure utility functions with no state (like `Math.abs()`, `Collections.sort()`)
- Factory methods that create objects
- Constants (`static final`)

---

## Instance Methods

An instance method belongs to an **object**. It can read and modify the object's fields. You must create an object first, then call the method on it.

```java
public class BankAccount {
    private double balance;    // instance field — each account has its own

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    // Instance method — works with this object's balance
    public void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Must be positive");
        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount > balance) throw new IllegalStateException("Insufficient funds");
        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}

// Must create an OBJECT before calling instance methods
BankAccount account = new BankAccount(1000.0);
account.deposit(500.0);                     // called on the instance
System.out.println(account.getBalance());   // 1500.0

BankAccount savings = new BankAccount(200.0);
savings.deposit(100.0);
System.out.println(savings.getBalance());   // 300.0  — different object, different balance
```

Each `BankAccount` object has its own `balance`. The instance method works on the object it is called on.

---

## Side-by-Side Comparison

| | Static method | Instance method |
|---|---|---|
| Declared with | `static` keyword | No `static` keyword |
| Called on | Class name: `MathUtils.square(5)` | Object: `account.deposit(500)` |
| Access to instance fields | ❌ No | ✅ Yes |
| Can be overridden | ❌ No | ✅ Yes |
| Requires an object | ❌ No | ✅ Yes |
| Use for | Stateless utilities | Object behaviour |

---

## Amalitech §8 — Prefer Instance Methods

> 📖 **Official Amalitech Standard:** §8 — "Prefer instance methods to static methods, so behaviour can be replaced and tested."

Static methods cannot be overridden and are hard to mock in tests. If your method needs to be substituted in a test (e.g. a fake email sender), it must be an instance method on a class that implements an interface.

**Rule of thumb:**
- If the method needs access to object state → instance method.
- If the method is a pure, stateless utility that will never need to be swapped → static is fine.

---

## Common Mistake: Calling an Instance Method Statically

```java
public class Counter {
    private int count = 0;

    public void increment() {
        count++;
    }
}

// BAD — cannot call instance method without an object:
Counter.increment();  // Compile error

// GOOD:
Counter c = new Counter();
c.increment();
```

---

## Exercises

**Exercise 1:** Create a `StringUtils` class with static methods: `reverse(String s)`, `isPalindrome(String s)`, and `countVowels(String s)`. Call all three from `main()`.

**Exercise 2:** Create a `Temperature` class with an instance field `degrees` and instance methods `toCelsius()` and `toFahrenheit()`. Create two Temperature objects and convert each. Show that they maintain separate state.

**Exercise 3:** Explain why `main()` is declared `static`. What would happen if it were an instance method? Write a one-paragraph explanation in a comment at the top of a file called `WhyMainIsStatic.java`.
