# Phase 14 Exercises — Software Engineering Practices

These exercises require you to refactor code, apply SOLID principles, and implement design patterns. Many start with "bad" code that you must fix.

---

## Exercise 1 — Naming Refactor (§3)

Rename every identifier in the following class to follow the Amalitech naming conventions:

```java
class mgr {
    private List<Map<String, Object>> dt = new ArrayList<>();

    void addRec(String n, int a, double s) {
        Map<String, Object> m = new HashMap<>();
        m.put("n", n);
        m.put("a", a);
        m.put("s", s);
        dt.add(m);
    }

    double calcAvgSal() {
        double tot = 0;
        for (Map<String, Object> r : dt) {
            tot += (double) r.get("s");
        }
        return dt.isEmpty() ? 0 : tot / dt.size();
    }
}
```

**Requirements:**
1. Rename the class and all variables to follow §3 conventions.
2. Replace the `Map<String, Object>` with a proper Java `record`.
3. No single-letter variables (except loop counters).
4. No noise words in class names.
5. Use a named constant for any magic number.

---

## Exercise 2 — Extract Method (§4.1, §4.2)

The following method is too long and does multiple things. Refactor it.

```java
class InvoiceProcessor {
    void processInvoice(String customerId, List<Map<String, Double>> lineItems, boolean isPremium) {
        // validate
        if (customerId == null || customerId.isBlank()) {
            throw new IllegalArgumentException("Customer ID required");
        }
        if (lineItems == null || lineItems.isEmpty()) {
            throw new IllegalArgumentException("Invoice must have at least one item");
        }

        // calculate subtotal
        double subtotal = 0;
        for (Map<String, Double> item : lineItems) {
            subtotal += item.get("price") * item.get("quantity");
        }

        // apply discount
        double discount = 0;
        if (isPremium) {
            discount = subtotal * 0.15;
        } else if (subtotal > 1000) {
            discount = subtotal * 0.05;
        }

        // calculate tax
        double taxableAmount = subtotal - discount;
        double tax = taxableAmount * 0.20;

        // calculate total
        double total = taxableAmount + tax;

        // print invoice
        System.out.println("=== INVOICE ===");
        System.out.println("Customer: " + customerId);
        System.out.printf("Subtotal: %.2f%n", subtotal);
        System.out.printf("Discount: %.2f%n", discount);
        System.out.printf("Tax (20%%): %.2f%n", tax);
        System.out.printf("TOTAL: %.2f%n", total);
    }
}
```

**Requirements:**
1. Use a `record` or class for line items instead of `Map<String, Double>`.
2. Extract validation into a separate private method.
3. Extract subtotal calculation into a private method.
4. Extract discount calculation into a private method.
5. Extract tax calculation into a private method.
6. Extract printing into a private method.
7. The main `processInvoice` method should read like an outline: call each step in sequence.
8. Remove the `boolean isPremium` flag argument by extracting two separate methods (§4.5).

---

## Exercise 3 — Single Responsibility Principle (§12.1)

This class violates SRP:

```java
class ReportService {

    void generateReport(List<Employee> employees, String format) {
        // validate
        if (employees == null || employees.isEmpty()) {
            throw new IllegalArgumentException("No employees provided");
        }

        // compute stats
        double avgSalary = employees.stream()
            .mapToDouble(Employee::getSalary)
            .average()
            .orElse(0);

        Employee highest = employees.stream()
            .max(Comparator.comparing(Employee::getSalary))
            .orElseThrow();

        // format output
        String report;
        if (format.equals("TEXT")) {
            report = "SALARY REPORT\n" +
                     "Average: " + avgSalary + "\n" +
                     "Highest: " + highest.getName() + " (" + highest.getSalary() + ")";
        } else if (format.equals("CSV")) {
            report = "metric,value\naverage," + avgSalary + "\nhighest_name," +
                     highest.getName() + "\nhighest_salary," + highest.getSalary();
        } else {
            throw new IllegalArgumentException("Unsupported format: " + format);
        }

        // save to file
        try {
            Files.writeString(Path.of("report." + format.toLowerCase()), report);
        } catch (IOException e) {
            throw new RuntimeException("Could not write report", e);
        }

        // send email notification
        System.out.println("Email sent: report.pdf attached");
    }
}
```

**Task:**
1. Identify all responsibilities in this class.
2. Create a separate class for each responsibility.
3. Create a `ReportOrchestrator` that coordinates them.
4. Each class should have a single, clear reason to change.

---

## Exercise 4 — Open/Closed Principle (§12.2)

```java
class ShippingCalculator {
    double calculateShipping(Order order, String shippingType) {
        if (shippingType.equals("STANDARD")) {
            return order.getWeight() * 2.50;
        } else if (shippingType.equals("EXPRESS")) {
            return order.getWeight() * 5.00 + 10.00;
        } else if (shippingType.equals("OVERNIGHT")) {
            return order.getWeight() * 8.00 + 25.00;
        }
        throw new IllegalArgumentException("Unknown shipping type: " + shippingType);
    }
}
```

**Task:**
1. Create a `ShippingStrategy` interface.
2. Create a class for each shipping type.
3. Refactor `ShippingCalculator` to use the interface.
4. Add a new `INTERNATIONAL` shipping type (base rate $50 + $12 per kg) **without modifying `ShippingCalculator`** — only add new classes.

---

## Exercise 5 — Liskov Substitution Principle (§12.3)

Identify and fix the LSP violation:

```java
class Account {
    protected double balance;

    void deposit(double amount) {
        balance += amount;
    }

    void withdraw(double amount) {
        balance -= amount;
    }

    double getBalance() {
        return balance;
    }
}

class FixedDepositAccount extends Account {
    @Override
    void withdraw(double amount) {
        throw new UnsupportedOperationException("Cannot withdraw from a fixed deposit account");
    }
}
```

**Task:**
1. Show why this violates LSP (write the code that breaks when you substitute `FixedDepositAccount` for `Account`).
2. Redesign the hierarchy using interfaces to separate accounts that support withdrawal from those that do not.
3. The solution should not use `UnsupportedOperationException` anywhere.

---

## Exercise 6 — Interface Segregation Principle (§12.4)

```java
interface ReportGenerator {
    void generatePdfReport(List<Employee> employees);
    void generateCsvReport(List<Employee> employees);
    void generateExcelReport(List<Employee> employees);
    void sendReportByEmail(String recipient, byte[] report);
    void saveReportToDatabase(String reportName, byte[] report);
}
```

A `SimpleReportService` only needs to generate PDF and CSV, not Excel, email, or database storage. A `FullReportService` needs everything.

**Task:**
1. Split `ReportGenerator` into focused interfaces.
2. Implement `SimpleReportService` and `FullReportService` using the appropriate interfaces.
3. No class should implement methods it does not use.

---

## Exercise 7 — Dependency Inversion Principle (§12.5)

```java
class NotificationScheduler {
    private final SmtpEmailSender emailSender = new SmtpEmailSender("smtp.gmail.com", 587);

    void scheduleReminders(List<Task> tasks) {
        for (Task task : tasks) {
            if (task.getDueDate().isBefore(LocalDate.now().plusDays(1))) {
                emailSender.sendReminder(task.getAssigneeEmail(), task.getTitle());
            }
        }
    }
}
```

**Task:**
1. Create a `ReminderSender` interface.
2. Create `SmtpEmailReminderSender` and `SmsReminderSender` implementing the interface.
3. Refactor `NotificationScheduler` to depend on `ReminderSender` through constructor injection.
4. Show how to test `NotificationScheduler` without sending real emails (use a simple test double that records calls).

---

## Exercise 8 — Apply the Builder Pattern

You have this order creation code scattered through the codebase:

```java
Order order = new Order();
order.setCustomerId("CUST-001");
order.setItems(List.of(new OrderItem("SKU-A", 2, 29.99)));
order.setShippingAddress("123 Main St, Accra");
order.setShippingType("EXPRESS");
order.setCouponCode("SAVE10");
order.setGiftMessage("Happy Birthday!");
order.setNotes("Leave at door");
```

**Task:**
1. Create an `Order` class with private fields (not settable after construction).
2. Create an `Order.Builder` with fluent methods.
3. Make `customerId` and `items` mandatory in the builder.
4. Make all other fields optional with sensible defaults.
5. Add validation in `build()` that throws if mandatory fields are missing.
6. Show how to construct the same order using the builder.

---

## Exercise 9 — Apply the Strategy Pattern

```java
class DataExporter {
    void export(List<Employee> employees, String format) {
        if (format.equals("JSON")) {
            // 40 lines of JSON serialization logic
        } else if (format.equals("CSV")) {
            // 30 lines of CSV serialization logic
        } else if (format.equals("XML")) {
            // 50 lines of XML serialization logic
        }
    }
}
```

**Task:**
1. Create an `ExportStrategy` interface: `String export(List<Employee> employees)`.
2. Create `JsonExportStrategy`, `CsvExportStrategy`, `XmlExportStrategy`.
3. Refactor `DataExporter` to use the strategy.
4. Show how to add a new `MarkdownExportStrategy` without touching `DataExporter`.

---

## Exercise 10 — Comprehensive Refactor

This is a real-world scenario. The following code works but violates multiple Amalitech standards. Your task is a full refactor.

```java
class x {
    static ArrayList<String[]> d = new ArrayList<>();

    static void add(String n, String e, String dep, double s) {
        if(n==null||n.isEmpty()||e==null||!e.contains("@")||s<0) {
            System.out.println("bad data");
            return;
        }
        d.add(new String[]{n,e,dep,String.valueOf(s)});
        System.out.println("added "+n);
    }

    static void printAll(boolean sorted) {
        ArrayList<String[]> copy = new ArrayList<>(d);
        if(sorted) {
            copy.sort((a,b)->a[0].compareTo(b[0]));
        }
        for(String[] r:copy) {
            System.out.println(r[0]+" | "+r[1]+" | "+r[2]+" | "+r[3]);
        }
    }

    static double avgSal() {
        if(d.isEmpty()) return 0;
        double t=0;
        for(String[] r:d) t+=Double.parseDouble(r[3]);
        return t/d.size();
    }

    public static void main(String[] args) {
        add("Alice Johnson","alice@co.com","Engineering",95000);
        add("Bob Smith","bob@co.com","Marketing",72000);
        add("","bad-email","",−1);
        printAll(true);
        System.out.println("Average: "+avgSal());
    }
}
```

**Requirements:**
1. Apply §3 naming conventions throughout.
2. Create an `Employee` record to replace `String[]`.
3. Apply SRP: separate validation, storage, display, and statistics.
4. Remove the flag argument `boolean sorted` per §4.5.
5. Remove the `static` data store — use an instance with a proper collection.
6. Use streams where appropriate.
7. Add a `@ControllerAdvice`-style approach: validation should throw a custom exception, not print "bad data".
8. The resulting code should follow the three-layer pattern: data model → service → main.

---

## Checklist

Before moving to Phase 15, confirm you can:

- [ ] Rename identifiers to follow Amalitech §3 without guidance
- [ ] Identify when a method is too long and extract it into smaller methods (§4.1)
- [ ] Apply SRP by identifying separate responsibilities and extracting classes (§12.1)
- [ ] Apply OCP using interfaces and new implementations instead of editing existing code (§12.2)
- [ ] Identify LSP violations and fix them by redesigning hierarchies (§12.3)
- [ ] Split fat interfaces into focused ones (§12.4)
- [ ] Apply DIP using constructor injection (§12.5)
- [ ] Implement the Builder pattern for complex objects
- [ ] Implement the Strategy pattern for interchangeable algorithms
- [ ] Write a Conventional Commits message for a given change
- [ ] Identify what makes a PR description good or bad
