# Custom Exceptions

## What is this concept?

A **custom exception** is an exception class you write yourself, tailored to your domain. Instead of throwing a generic `IllegalArgumentException`, you throw an `InvalidEmailException` that clearly communicates what went wrong and carries relevant context.

---

## Why is it important?

Generic exceptions are ambiguous. When a caller catches `RuntimeException`, they do not know if it is a programmer error, invalid input, or a business rule violation. Custom exceptions make error types explicit, enable targeted handling, and document your API's failure modes.

---

## Creating a Checked Exception (extends `Exception`)

Extend `Exception` for exceptions that callers must explicitly handle — typically infrastructure or business-critical failures.

```java
// A checked exception for domain-level failures
public class InsufficientFundsException extends Exception {

    private final double requested;
    private final double available;

    // Basic constructor with message
    public InsufficientFundsException(String message) {
        super(message);
        this.requested = 0;
        this.available = 0;
    }

    // Rich constructor — carry context
    public InsufficientFundsException(double requested, double available) {
        super(String.format(
            "Insufficient funds: requested %.2f but only %.2f available",
            requested, available));
        this.requested = requested;
        this.available = available;
    }

    // Constructor with cause — for exception chaining
    public InsufficientFundsException(String message, Throwable cause) {
        super(message, cause);
        this.requested = 0;
        this.available = 0;
    }

    public double getRequested() { return requested; }
    public double getAvailable() { return available; }
}
```

Usage:

```java
public void withdraw(double amount) throws InsufficientFundsException {
    if (amount > balance) {
        throw new InsufficientFundsException(amount, balance);
    }
    balance -= amount;
}

// Caller must handle it
try {
    account.withdraw(500);
} catch (InsufficientFundsException e) {
    System.err.println("Transaction failed: " + e.getMessage());
    System.err.println("You need: " + e.getRequested() + ", you have: " + e.getAvailable());
}
```

---

## Creating an Unchecked Exception (extends `RuntimeException`)

Extend `RuntimeException` for programming errors, invalid arguments, and state violations that should not normally occur in correct code.

```java
// An unchecked exception for invalid domain values
public class InvalidProductException extends RuntimeException {

    private final String productId;

    public InvalidProductException(String productId) {
        super("Product not found: " + productId);
        this.productId = productId;
    }

    public InvalidProductException(String productId, Throwable cause) {
        super("Product not found: " + productId, cause);
        this.productId = productId;
    }

    public String getProductId() { return productId; }
}
```

Usage:

```java
public Product findProduct(String productId) {
    Product product = productRepository.get(productId);
    if (product == null) {
        throw new InvalidProductException(productId);  // no throws declaration needed
    }
    return product;
}
```

---

## When to Use Checked vs Unchecked

| Situation | Use |
|---|---|
| Caller can reasonably recover (`catch` and do something useful) | Checked (`extends Exception`) |
| Programming error that should not occur in correct code | Unchecked (`extends RuntimeException`) |
| Infrastructure failure (I/O, network, DB) | Checked |
| Invalid argument passed to a method | Unchecked (`IllegalArgumentException` or custom) |
| Object used in wrong state | Unchecked (`IllegalStateException` or custom) |
| You are in doubt | Unchecked (modern Java prefers this) |

---

## Multiple Constructors — The Standard Pattern

Every custom exception should provide these four constructors for maximum flexibility:

```java
public class ApplicationException extends RuntimeException {

    // 1. No message
    public ApplicationException() {
        super();
    }

    // 2. Message only
    public ApplicationException(String message) {
        super(message);
    }

    // 3. Message + cause (exception chaining)
    public ApplicationException(String message, Throwable cause) {
        super(message, cause);
    }

    // 4. Cause only
    public ApplicationException(Throwable cause) {
        super(cause);
    }
}
```

---

## Exception Chaining — Preserving the Cause

When you catch one exception and throw another, always pass the original as the **cause**. This preserves the full diagnostic chain in the stack trace.

```java
// BAD — the original cause is lost forever
try {
    connection.query(sql);
} catch (SQLException e) {
    throw new DataAccessException("Query failed");  // original SQL error invisible
}

// GOOD — the cause is preserved
try {
    connection.query(sql);
} catch (SQLException e) {
    throw new DataAccessException("Query failed: " + sql, e);  // e is the cause
}
```

The preserved cause appears in the stack trace as `Caused by:`, giving you the full picture.

---

## Full Practical Example: Order Processing

```java
// Custom exception hierarchy
public class OrderException extends Exception {
    public OrderException(String message) { super(message); }
    public OrderException(String message, Throwable cause) { super(message, cause); }
}

public class OrderNotFoundException extends OrderException {
    private final String orderId;
    public OrderNotFoundException(String orderId) {
        super("Order not found: " + orderId);
        this.orderId = orderId;
    }
    public String getOrderId() { return orderId; }
}

public class OrderAlreadyShippedException extends OrderException {
    public OrderAlreadyShippedException(String orderId) {
        super("Order " + orderId + " has already been shipped and cannot be cancelled");
    }
}

// Service that throws them
public class OrderService {
    private final Map<String, Order> orders = new HashMap<>();

    public void cancelOrder(String orderId) throws OrderNotFoundException, OrderAlreadyShippedException {
        Order order = orders.get(orderId);

        if (order == null) {
            throw new OrderNotFoundException(orderId);
        }
        if (order.getStatus() == OrderStatus.SHIPPED) {
            throw new OrderAlreadyShippedException(orderId);
        }

        order.setStatus(OrderStatus.CANCELLED);
        System.out.println("Order " + orderId + " cancelled.");
    }
}

// Caller
public class Main {
    public static void main(String[] args) {
        OrderService service = new OrderService();

        try {
            service.cancelOrder("ORD-999");
        } catch (OrderNotFoundException e) {
            System.err.println("Cannot cancel: " + e.getMessage());
            // show user a "order not found" message
        } catch (OrderAlreadyShippedException e) {
            System.err.println("Cannot cancel: " + e.getMessage());
            // show user a different message
        } catch (OrderException e) {
            System.err.println("Order error: " + e.getMessage());
            // generic fallback
        }
    }
}
```

---

## Common Mistakes

**1. Extending `Exception` for every custom exception — forces callers to handle or declare**
```java
// If callers have nothing sensible to do, use RuntimeException instead
```

**2. Creating an exception without a message**
```java
throw new MyException();  // useless — no information about what went wrong
```

**3. Losing the cause**
```java
catch (IOException e) {
    throw new ServiceException("IO error");  // BAD — 'e' discarded
    throw new ServiceException("IO error", e);  // GOOD
}
```

**4. Too many exception types**
If you have 20 custom exceptions for one class, the API becomes hard to use. Group related failures under one base exception type.

---

## Key Takeaways

- Custom exceptions name the specific failure in your domain.
- Extend `Exception` for recoverable failures callers must handle; extend `RuntimeException` for programming errors.
- Always include a message. Always pass the cause when wrapping another exception.
- Build a small hierarchy: a base exception + specific subtypes.
- Never throw a bare `Exception` — always use the most specific type.

---

## Related Topics

- [try-catch-finally](../try-catch-finally/README.md) — catching custom exceptions
- [Best Practices](../best-practices/README.md) — what never to do
