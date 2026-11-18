# Spring Boot

Spring Boot is the most widely used framework for building Java backend applications. It eliminates the configuration overhead of the core Spring Framework and lets you go from zero to a running application in minutes.

---

## 1. What Is Spring Boot?

**Spring Framework** is a large collection of libraries for building Java applications. Its core feature is an **IoC (Inversion of Control) container** that manages objects and their dependencies.

**Spring Boot** is an opinionated layer on top of Spring Framework that:
- Auto-configures components based on what is on the classpath
- Provides starter dependencies (one dependency instead of ten)
- Embeds a web server (Tomcat) so you do not need to deploy to an external server
- Requires almost no XML configuration

The result: a production-ready application in a single runnable JAR file.

---

## 2. The IoC Container and Dependency Injection

The IoC container manages the lifecycle of objects called **beans**. Instead of your code creating its own dependencies with `new`, you declare what you need and the container provides it. This is **dependency injection (DI)**.

This is Amalitech §12.5 DIP in action: your code depends on abstractions (interfaces), and the container wires in the concrete implementation.

```
Traditional code:            Spring IoC:
  Service service =            @Autowired
    new ServiceImpl();         Service service;
  // you create it             // Spring creates and injects it
```

The benefit: you can swap implementations (e.g. a real database vs. a test double) without changing the class that uses them.

---

## 3. Creating a Maven Spring Boot Project

The easiest way is [start.spring.io](https://start.spring.io). Fill in:

| Field | Value |
|-------|-------|
| Project | Maven |
| Language | Java |
| Spring Boot | 3.3.x |
| Group | com.company |
| Artifact | employee-api |
| Java | 21 |
| Dependencies | Spring Web, Spring Data JPA, PostgreSQL Driver, Validation |

Download the zip, extract it, and open in IntelliJ.

### The Generated pom.xml

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.0</version>
</parent>

<dependencies>
    <!-- Web: REST controllers, embedded Tomcat -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- JPA: entity management, Spring Data repositories -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- PostgreSQL JDBC driver -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- Bean validation (@Valid, @NotNull, etc.) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- Tests -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

---

## 4. The Entry Point

Every Spring Boot application has one main class annotated with `@SpringBootApplication`.

```java
package com.company.employeeapi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class EmployeeApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(EmployeeApiApplication.class, args);
    }
}
```

`@SpringBootApplication` is a composite annotation combining:
- `@Configuration` — this class defines beans
- `@EnableAutoConfiguration` — activate Spring Boot's auto-configuration
- `@ComponentScan` — scan this package and sub-packages for Spring components

`SpringApplication.run()` starts the IoC container, auto-configures everything, and starts the embedded Tomcat server.

---

## 5. Spring Stereotype Annotations

These annotations mark classes as Spring-managed beans. The container creates one instance of each and makes it available for injection.

| Annotation | Layer | Purpose |
|------------|-------|---------|
| `@Component` | Any | Generic Spring bean |
| `@Service` | Business logic | Marks a service class |
| `@Repository` | Data access | Marks a data access class; also converts database exceptions |
| `@Controller` | Web / MVC | Handles HTTP requests (returns views) |
| `@RestController` | Web / REST | Like `@Controller` but every method returns JSON/XML (`@Controller` + `@ResponseBody`) |

```java
@Service
public class EmployeeService {
    // business logic
}

@Repository
public class EmployeeRepository {
    // data access
}

@RestController
public class EmployeeController {
    // HTTP handling
}
```

### The Three-Layer Architecture

```
HTTP Request
    ↓
@RestController    ← handles HTTP, calls service
    ↓
@Service           ← business logic, calls repository
    ↓
@Repository        ← data access (database)
    ↓
Database
```

Each layer depends on the layer below through an interface — directly applying Amalitech §12.5 DIP and §12.1 SRP.

---

## 6. Dependency Injection

Spring Boot supports three styles of injection. **Constructor injection** is the Amalitech-recommended approach (§12.5):

```java
// Constructor injection — PREFERRED (Amalitech §12.5 DIP)
@RestController
public class EmployeeController {

    private final EmployeeService employeeService; // injected

    public EmployeeController(EmployeeService employeeService) {
        this.employeeService = employeeService;
    }
}
```

Why prefer constructor injection:
- Makes dependencies explicit and mandatory
- Enables immutability (`final` field)
- Makes the class easy to test without Spring (just pass a mock)
- Avoids NPE from an uninitialized `@Autowired` field

Field injection (using `@Autowired` on a field) is discouraged because it hides dependencies and makes testing harder.

---

## 7. application.properties

Spring Boot reads `src/main/resources/application.properties` for configuration.

```properties
# Database connection
spring.datasource.url=jdbc:postgresql://localhost:5432/company_db
spring.datasource.username=postgres
spring.datasource.password=your_password
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA / Hibernate settings
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect

# Server port
server.port=8080

# Application name
spring.application.name=employee-api
```

`spring.jpa.hibernate.ddl-auto` options:
| Value | Effect |
|-------|--------|
| `create` | Drop and recreate schema on startup (dev only) |
| `update` | Update schema to match entities (dev) |
| `validate` | Validate schema matches entities; fail if not (prod) |
| `none` | Do nothing (use Flyway/Liquibase instead) |

---

## 8. Spring Data JPA

Spring Data JPA provides a repository abstraction that eliminates most boilerplate database code. You define an interface; Spring generates the implementation at runtime.

### @Entity

An entity is a Java class mapped to a database table.

```java
import jakarta.persistence.*;
import java.time.LocalDate;

@Entity
@Table(name = "employees")
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "first_name", nullable = false, length = 100)
    private String firstName;

    @Column(name = "last_name", nullable = false, length = 100)
    private String lastName;

    @Column(unique = true, nullable = false)
    private String email;

    private Double salary;

    @Column(name = "hire_date")
    private LocalDate hireDate;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id")
    private Department department;

    // constructors, getters, setters
    protected Employee() {} // required by JPA

    public Employee(String firstName, String lastName, String email, Double salary) {
        this.firstName = firstName;
        this.lastName  = lastName;
        this.email     = email;
        this.salary    = salary;
        this.hireDate  = LocalDate.now();
    }

    // getters and setters ...
}
```

Key annotations:
| Annotation | Purpose |
|-----------|---------|
| `@Entity` | Marks class as a JPA entity |
| `@Table(name = "...")` | Maps to a specific table name |
| `@Id` | Marks the primary key field |
| `@GeneratedValue` | Auto-generates the primary key |
| `@Column` | Configures column mapping, constraints |
| `@ManyToOne`, `@OneToMany` | Relationship mappings |

### JpaRepository

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {

    // Spring Data generates SQL from method names
    List<Employee> findByDepartment_Id(Long departmentId);
    List<Employee> findBySalaryGreaterThan(Double salary);
    Optional<Employee> findByEmail(String email);
    List<Employee> findByLastNameContainingIgnoreCase(String namePart);

    // Custom JPQL query
    @Query("SELECT e FROM Employee e WHERE e.salary > :min ORDER BY e.salary DESC")
    List<Employee> findHighEarners(@Param("min") Double minimumSalary);
}
```

`JpaRepository<Employee, Long>` provides out of the box:
- `save(entity)` — insert or update
- `findById(id)` — returns `Optional<Employee>`
- `findAll()` — returns `List<Employee>`
- `deleteById(id)` — delete by primary key
- `count()` — number of rows
- `existsById(id)` — boolean check

---

## 9. Transactions with @Transactional

Spring manages transactions declaratively with `@Transactional`. When a method annotated with `@Transactional` is called, Spring opens a transaction before the method and commits when it returns. If an unchecked exception is thrown, it rolls back automatically.

```java
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(EmployeeRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public Employee create(Employee employee) {
        return repository.save(employee);
    }

    @Transactional(readOnly = true)
    public List<Employee> findAll() {
        return repository.findAll();
    }

    @Transactional
    public Employee update(Long id, Employee updated) {
        Employee existing = repository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("Employee not found: " + id));
        existing.setFirstName(updated.getFirstName());
        existing.setLastName(updated.getLastName());
        existing.setSalary(updated.getSalary());
        return repository.save(existing); // save is not strictly required for managed entities
    }

    @Transactional
    public void delete(Long id) {
        if (!repository.existsById(id)) {
            throw new EntityNotFoundException("Employee not found: " + id);
        }
        repository.deleteById(id);
    }
}
```

`readOnly = true` on read methods is a performance hint: Hibernate skips dirty checking, and some databases optimize read-only transactions.

---

## Key Takeaways

- Spring Boot auto-configures a Spring application. One main class with `@SpringBootApplication` and `SpringApplication.run()` is all you need to start.
- `@Component`, `@Service`, `@Repository`, `@RestController` mark classes as Spring-managed beans.
- Constructor injection is the preferred style (Amalitech §12.5 DIP). It makes dependencies explicit and testable.
- `application.properties` holds database URLs, credentials, and JPA settings.
- `@Entity` maps a Java class to a database table. `JpaRepository` provides CRUD without boilerplate SQL.
- `@Transactional` manages database transactions declaratively.
