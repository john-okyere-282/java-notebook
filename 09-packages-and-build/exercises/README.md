# Phase 09 Exercises — Packages and Build Tools

---

## Exercise 1 — Organise Existing Code Into Packages

Take the following unpackaged classes (write them out) and reorganise them into a proper package structure following AmaliTech §3.5:

- `Customer.java` — a data class with name, email, customerId
- `Order.java` — a data class with orderId, customerId, totalAmount
- `CustomerService.java` — business logic: add customer, find by id
- `OrderService.java` — business logic: place order, cancel order
- `DatabaseConnection.java` — utility class for connection management

Requirements:
1. Choose the project name `ecommerce`.
2. Organise by feature (not by layer).
3. Write the full `package` declaration for each class.
4. Write the correct `import` statements needed in `OrderService` to use `Order` and `Customer`.
5. Explain why wildcard imports are avoided (AmaliTech §11.1).

---

## Exercise 2 — Create a Maven Project

Create a Maven project from scratch (by hand — no IDE wizard) for a library management system:

1. Create the directory structure: `src/main/java/com/amalitech/library/` and `src/test/java/com/amalitech/library/`.
2. Write a `pom.xml` targeting Java 21 with JUnit 5 and Mockito as test dependencies.
3. Create a `Book.java` class in the correct package.
4. Create a `BookTest.java` test class.
5. Run `mvn compile` and `mvn test` and confirm they pass.

---

## Exercise 3 — Add and Use a Dependency

Starting from the Maven project in Exercise 2:

1. Add the **Apache Commons Lang** dependency (`org.apache.commons:commons-lang3:3.14.0`) to `pom.xml`.
2. Use `StringUtils.capitalize()` from that library in a method in `Book.java` to capitalise the book title.
3. Run `mvn dependency:tree` and find `commons-lang3` in the output.
4. Write a test verifying the capitalisation behaviour.

---

## Exercise 4 — Maven Build Lifecycle

Answer these questions, then verify each answer by running the commands:

1. What command compiles the code without running tests?
2. What command creates the JAR file?
3. What command cleans the build output?
4. What is the correct command to use in a CI pipeline to get a clean, tested build?
5. What directory contains the compiled classes after `mvn compile`?
6. Where does the JAR file appear after `mvn package`?

Then: add a `main` method to your `Book.java` and configure `maven-jar-plugin` to set it as the main class. Run the JAR with `java -jar`.

---

## Exercise 5 — Feature vs Layer Package Structure

You are given the brief for a hospital management system with these features:
- Patient registration and lookup
- Doctor scheduling
- Appointment booking
- Billing

1. Design a **by-layer** package structure for this system.
2. Design a **by-feature** package structure for the same system.
3. For each structure, list the full package names for at least 10 classes.
4. Write a paragraph explaining which structure you would choose and why.
5. If the hospital later decides to extract "billing" into a separate microservice, which structure makes that easier? Explain.
