# Java Packages

## What Are Packages?

A package is a named namespace that groups related Java classes, interfaces, and enums. Packages serve three purposes:

1. **Organisation** — related classes live together, unrelated classes stay apart.
2. **Namespace management** — two classes can have the same simple name as long as they are in different packages (`com.amalitech.user.User` and `com.external.User` do not conflict).
3. **Access control** — the `protected` and package-private (no modifier) access levels are defined relative to packages.

---

## Package Declaration

The `package` statement must be the **first non-comment line** in a source file.

```java
package com.amalitech.inventory.service;

public class InventoryService {
    // ...
}
```

The package name must match the directory structure on disk:
```
src/
└── main/
    └── java/
        └── com/
            └── amalitech/
                └── inventory/
                    └── service/
                        └── InventoryService.java
```

---

## Import Statements

To use a class from another package, import it.

### Single-class import (required by AmaliTech §11.1)

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
```

### Wildcard import (forbidden — AmaliTech §11.1)

```java
// BAD — do not use this
import java.util.*;
```

Wildcard imports pollute the namespace, make it unclear which classes are used, and cause problems when two packages have a class with the same name. AmaliTech coding standards (§11.1) explicitly prohibit wildcard imports.

### Static imports

Static imports are acceptable for test assertions and constants, used sparingly.

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static java.lang.Math.PI;
```

### No import needed

Classes in the same package and all classes in `java.lang` (like `String`, `System`, `Math`) are available without import.

---

## Package Naming Convention

> **AmaliTech Standard §3.5:** Package names follow the reverse-domain convention: `com.amalitech.<projectname>`

Rules:
- All lowercase — no uppercase letters, no underscores.
- Reverse domain name as the root: `com.amalitech`
- Then the project/product name: `com.amalitech.inventory`
- Then sub-packages by feature or layer: `com.amalitech.inventory.service`

```
com.amalitech.hrms.employee.repository
com.amalitech.hrms.employee.service
com.amalitech.hrms.employee.dto
com.amalitech.hrms.payroll.service
```

---

## Organising Packages: Feature vs Layer

There are two common structures. Feature-based is generally preferred.

### By Layer (traditional)

```
com.amalitech.app
├── controller
│   ├── UserController.java
│   └── OrderController.java
├── service
│   ├── UserService.java
│   └── OrderService.java
├── repository
│   ├── UserRepository.java
│   └── OrderRepository.java
└── model
    ├── User.java
    └── Order.java
```

**Pros:** Easy to understand at a glance which layer a class is in.  
**Cons:** Working on one feature requires touching four different packages.

### By Feature (recommended)

```
com.amalitech.app
├── user
│   ├── UserController.java
│   ├── UserService.java
│   ├── UserRepository.java
│   └── User.java
└── order
    ├── OrderController.java
    ├── OrderService.java
    ├── OrderRepository.java
    └── Order.java
```

**Pros:** A single feature is self-contained in one package. Easier to extract into a module later.  
**Cons:** Less obvious which classes are controllers vs services at a glance.

---

## Access Rules Between Packages

| Modifier | Same Class | Same Package | Subclass (different package) | Anywhere |
|----------|-----------|--------------|------------------------------|----------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| *(none — package-private)* | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

Package-private (no access modifier) is the default for classes and members. It means the class or member is accessible to all code in the same package, but not outside it.

```java
package com.amalitech.inventory;

class InternalHelper {  // package-private class — only usable inside this package
    static void doSomething() { }
}
```

---

## The Default Package

If you omit the `package` statement, the class belongs to the unnamed default package. Never use the default package in production code — classes in the default package cannot be imported by classes in named packages.

---

## Complete Example

**File:** `src/main/java/com/amalitech/library/book/Book.java`

```java
package com.amalitech.library.book;

public class Book {
    private final String isbn;
    private final String title;
    private final String author;

    public Book(String isbn, String title, String author) {
        this.isbn = isbn;
        this.title = title;
        this.author = author;
    }

    public String getIsbn()   { return isbn; }
    public String getTitle()  { return title; }
    public String getAuthor() { return author; }

    @Override
    public String toString() {
        return String.format("Book[isbn=%s, title=%s, author=%s]", isbn, title, author);
    }
}
```

**File:** `src/main/java/com/amalitech/library/catalog/Catalog.java`

```java
package com.amalitech.library.catalog;

import com.amalitech.library.book.Book;  // explicit import — not a wildcard

import java.util.ArrayList;
import java.util.List;

public class Catalog {
    private final List<Book> books = new ArrayList<>();

    public void addBook(Book book) {
        books.add(book);
    }

    public List<Book> getAllBooks() {
        return List.copyOf(books);
    }
}
```
