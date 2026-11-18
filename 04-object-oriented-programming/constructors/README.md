# Constructors

## What is a Constructor?
A constructor is a special method called automatically when you create an object with `new`. Its job is to set the object's initial state.

**Rules:**
- Has the same name as the class
- Has no return type (not even `void`)
- Called automatically with `new ClassName(...)`
- A class can have multiple constructors (overloading)

---

## Default Constructor vs Parameterised Constructor

```java
public class Person {
    private String name;
    private int age;

    // Default constructor (no parameters)
    public Person() {
        this.name = "Unknown";
        this.age = 0;
    }

    // Parameterised constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return name + " (age " + age + ")";
    }
}

// Usage
Person p1 = new Person();                // calls default constructor
Person p2 = new Person("Alice", 30);     // calls parameterised constructor

System.out.println(p1);  // Unknown (age 0)
System.out.println(p2);  // Alice (age 30)
```

---

## The this Keyword

Inside a constructor (or any instance method), `this` refers to the current object being constructed.

Its most common use is to distinguish between a parameter and a field when they share the same name:

```java
public Person(String name, int age) {
    this.name = name;  // this.name = instance field; name = constructor parameter
    this.age = age;
}
```

Without `this`, `name = name` would just assign the parameter to itself — the field would remain at its default value.

---

## Constructor Chaining with this()

One constructor can call another in the same class using `this(...)`. This must be the **first statement** in the constructor body.

```java
public class Product {
    private String name;
    private double price;
    private int quantity;

    // Full constructor
    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    // Convenience constructor — calls the full constructor
    public Product(String name, double price) {
        this(name, price, 0);  // quantity defaults to 0
    }

    // Minimal constructor
    public Product(String name) {
        this(name, 0.0);  // price defaults to 0.0
    }

    @Override
    public String toString() {
        return String.format("%s — $%.2f (qty: %d)", name, price, quantity);
    }
}

Product p1 = new Product("Laptop", 999.99, 5);
Product p2 = new Product("Mouse", 29.99);       // quantity = 0
Product p3 = new Product("Cable");              // price = 0.0, quantity = 0

System.out.println(p1);  // Laptop — $999.99 (qty: 5)
System.out.println(p2);  // Mouse — $29.99 (qty: 0)
System.out.println(p3);  // Cable — $0.00 (qty: 0)
```

Constructor chaining avoids duplicating initialisation logic across multiple constructors.

---

## The Default Constructor

If you write **no** constructors at all, Java provides a **default no-arg constructor** automatically. It sets fields to their default values (`0`, `false`, `null`).

**Important:** As soon as you write **any** constructor yourself, Java no longer provides the default one. If you still need a no-arg constructor, write it explicitly.

```java
public class Widget {
    // No constructor written — Java provides: public Widget() {}
}

public class Gadget {
    private String model;

    public Gadget(String model) {
        this.model = model;
    }
    // The default no-arg constructor is GONE.
    // new Gadget() would be a compile error!
}
```

---

## Validation in Constructors

Constructors are the right place to enforce invariants — conditions that must always be true for the object:

```java
public class Rectangle {
    private double width;
    private double height;

    public Rectangle(double width, double height) {
        if (width <= 0 || height <= 0) {
            throw new IllegalArgumentException(
                "Dimensions must be positive. Got: " + width + " x " + height);
        }
        this.width = width;
        this.height = height;
    }

    public double area() {
        return width * height;
    }
}

// new Rectangle(-5, 10) throws IllegalArgumentException immediately
// No Rectangle object can ever exist with invalid dimensions
```

---

## Best Practices

- **Keep constructors simple** — only set fields, validate inputs, and chain to other constructors. Don't put complex business logic in a constructor (Amalitech §4.2).
- **Validate early** — throw `IllegalArgumentException` or `NullPointerException` in the constructor for invalid inputs. It is much better to fail at construction time than to get a mysterious error later.
- **Use chaining to avoid duplication** — if two constructors share logic, have one call the other.

---

## Exercises

**Exercise 1:** Create a `Circle` class with a `radius` field. Write a constructor that validates the radius is positive. Add `area()` and `circumference()` methods. Test with valid and invalid inputs.

**Exercise 2:** Create a `Book` class with fields: `title`, `author`, `year`, `pages`. Write a full constructor and a convenience constructor that takes only `title` and `author` (default year to current year, pages to 0). Override `toString()`.

**Exercise 3:** Create a `Student` class. Write three constructors using chaining: one with name, id, and gpa; one with name and id (gpa defaults to 0.0); one with name only (id defaults to `"TBD"`).

**Exercise 4:** Create a `BankAccount` class with an `owner` and `balance` field. The constructor should throw `IllegalArgumentException` if the initial balance is negative. Test that the exception is thrown.

**Exercise 5:** Demonstrate what happens when you have a parameterised constructor but no default constructor. Write a `Vehicle` class with one constructor that takes a brand. Then try `new Vehicle()` and explain the compile error in a comment.
