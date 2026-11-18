# Polymorphism

## What is Polymorphism?
"Polymorphism" means "many forms." The same method name behaves differently depending on the object it is called on. This lets you write code that works with many different types through a single, consistent interface.

**Analogy:** You say "speak" to a Dog and it barks. You say "speak" to a Cat and it meows. You say "speak" to a Parrot and it repeats a phrase. The command is the same; the behaviour depends on the animal.

---

## Two Kinds of Polymorphism

| Kind | Also called | When resolved | Mechanism |
|------|------------|---------------|-----------|
| Compile-time | Static polymorphism, Method overloading | At compile time | Same name, different signatures |
| Runtime | Dynamic polymorphism, Method overriding | At runtime | Subclass overrides parent method |

---

## Compile-Time Polymorphism (Method Overloading)
Already covered in Phase 03. Same method name, different parameter types. Java picks the right version at compile time.

```java
public static void print(int n)    { System.out.println("int: " + n); }
public static void print(double d) { System.out.println("double: " + d); }
public static void print(String s) { System.out.println("String: " + s); }

print(42);       // int: 42
print(3.14);     // double: 3.14
print("hello");  // String: hello
```

---

## Runtime Polymorphism (Method Overriding)

```java
public class Animal {
    public String speak() {
        return "...";
    }
}

public class Dog extends Animal {
    @Override
    public String speak() {
        return "Woof!";
    }
}

public class Cat extends Animal {
    @Override
    public String speak() {
        return "Meow!";
    }
}

public class Parrot extends Animal {
    @Override
    public String speak() {
        return "Polly wants a cracker!";
    }
}
```

```java
// Polymorphism in action — the variable type is Animal,
// but the ACTUAL object is Dog, Cat, or Parrot
Animal[] animals = { new Dog(), new Cat(), new Parrot(), new Dog() };

for (Animal animal : animals) {
    System.out.println(animal.speak());   // correct version called at runtime
}
// Output:
// Woof!
// Meow!
// Polly wants a cracker!
// Woof!
```

Java looks at the **actual runtime type** of the object to decide which `speak()` to call — not the declared type of the variable.

---

## Why This Is Powerful

You can write one method that works for any current or future subtype:

```java
public void processPayment(Payment payment) {
    payment.validate();
    payment.process();
    payment.sendReceipt();
}
```

This works for `CreditCardPayment`, `PayPalPayment`, `BankTransfer`, or any `Payment` subclass you add in the future — without changing `processPayment()`. That is the **Open/Closed Principle** (open for extension, closed for modification).

---

## Upcasting and Downcasting

**Upcasting** — storing a subtype in a parent-type variable. Automatic and always safe:
```java
Animal animal = new Dog();   // upcast — always works
```

**Downcasting** — converting back to a specific subtype. Manual and can fail at runtime:
```java
Animal animal = new Dog();
Dog dog = (Dog) animal;      // downcast — works here because it IS a Dog
dog.fetch("ball");

Cat cat = (Cat) animal;      // ClassCastException! — it's a Dog, not a Cat
```

---

## instanceof Check (Always Before Downcasting)

Always check with `instanceof` before downcasting:

```java
if (animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.fetch("ball");
}
```

**Java 16+ — Pattern Matching (cleaner):**
```java
// Combines the check and the cast in one line
if (animal instanceof Dog dog) {
    dog.fetch("ball");   // 'dog' is already the correctly typed variable
}
```

---

## Practical Use Case — Shape Renderer

```java
public class Circle extends Shape {
    @Override
    public double area() { return Math.PI * radius * radius; }
}

public class Rectangle extends Shape {
    @Override
    public double area() { return width * height; }
}

public class Triangle extends Shape {
    @Override
    public double area() { return 0.5 * base * height; }
}

// Works for every Shape — now and in the future
public static void printAreas(List<Shape> shapes) {
    for (Shape shape : shapes) {
        System.out.printf("Area: %.2f%n", shape.area());
    }
}
```

---

## Common Mistake — Calling a Subclass-Only Method

```java
Animal animal = new Dog();
animal.fetch("ball");   // COMPILE ERROR — Animal has no fetch() method

// Fix:
if (animal instanceof Dog dog) {
    dog.fetch("ball");
}
```

The declared type (`Animal`) determines which methods you can call. The actual type determines which implementation runs.

---

## Exercises

**Exercise 1:** Create a `Shape` parent class with an abstract `area()` method. Subclasses: `Circle`, `Rectangle`, `Triangle`. Put one of each in a `List<Shape>` and use a loop to print all areas. Add a fourth shape later and show no existing code needs to change.

**Exercise 2:** Create an `Employee` class with a `calculatePay()` method. `FullTimeEmployee` has a fixed monthly salary. `PartTimeEmployee` has an hourly rate × hours worked. Store both in a `List<Employee>` and calculate total payroll.

**Exercise 3:** Demonstrate `instanceof` and downcasting: given a `List<Animal>` containing `Dog`s and `Cat`s, loop through and call `fetch()` only on dogs and `purr()` only on cats. Use Java 16 pattern matching if available.

**Exercise 4:** Create a `Printer` class that has a `print(Document doc)` method. `Document` is a parent class. Subclasses: `PDFDocument` and `WordDocument` with different `print()` implementations. Show that `Printer` doesn't need to change when you add `SpreadsheetDocument`.

**Exercise 5:** Explain (in comments) why this would be a problem and how polymorphism solves it:
```java
// Without polymorphism — adding a new animal type requires changing this method
public void makeAllAnimalsSpeak(Dog[] dogs, Cat[] cats, Parrot[] parrots) {
    for (Dog d : dogs) System.out.println(d.bark());
    for (Cat c : cats) System.out.println(c.meow());
    for (Parrot p : parrots) System.out.println(p.talk());
}
```
