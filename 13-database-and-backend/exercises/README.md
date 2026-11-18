# Phase 13 Exercises — Database and Backend Development

Work through these exercises in order. The first few build SQL skills; later exercises combine JDBC and Spring Boot. Attempt each exercise independently before looking for help.

---

## Exercise 1 — SQL: Design a Library Schema

**Concept:** Database design, primary keys, foreign keys, normalization

Design a relational schema for a library management system. Write the `CREATE TABLE` statements for the following entities:

- `books` — isbn (primary key), title, author, year_published, available_copies
- `members` — id, first_name, last_name, email (unique), join_date
- `loans` — id, book_isbn (foreign key), member_id (foreign key), loan_date, return_date (nullable)

**Requirements:**
1. Write all three `CREATE TABLE` statements with appropriate types and constraints.
2. Add a `CHECK` constraint ensuring `available_copies >= 0`.
3. Add an `INDEX` on `books.author` to support fast searches by author.

**What to submit:** The full SQL script.

---

## Exercise 2 — SQL: Query the Employee Database

**Concept:** SELECT, WHERE, JOIN, GROUP BY, ORDER BY, aggregate functions

Using the employee database from the SQL Basics chapter (employees + departments tables), write SQL queries for:

1. All employees ordered by salary (highest first).
2. Employees in the Engineering department.
3. Employees hired after January 1, 2022.
4. The average salary by department, with the department name.
5. The department with the highest average salary.
6. All employees earning above the company average salary.
7. Employees with the word "John" in their first or last name.
8. The total salary bill per department.
9. Any department with more than 2 employees.
10. The employee with the highest salary in each department.

---

## Exercise 3 — SQL: DML Operations

**Concept:** INSERT, UPDATE, DELETE

Write SQL statements to:

1. Insert three new employees into the employee database.
2. Give all Engineering employees a 10% raise.
3. Move all Marketing employees to a new department called "Growth".
4. Delete all employees who were hired before January 1, 2019.
5. Update a specific employee's email address.

---

## Exercise 4 — JDBC: Connect and Query

**Concept:** JDBC connection, Statement, ResultSet, try-with-resources

Write a Java class `EmployeeReader` with a method:
```java
public List<String> getEmployeeNames() throws SQLException
```

Requirements:
- Connect to PostgreSQL using `DriverManager`.
- Execute a `SELECT first_name, last_name FROM employees ORDER BY last_name`.
- Return a `List<String>` where each entry is `"LastName, FirstName"`.
- Use `try-with-resources` for all JDBC resources.
- Do not use `PreparedStatement` for this exercise (there is no user input).

---

## Exercise 5 — JDBC: PreparedStatement CRUD

**Concept:** PreparedStatement, SQL injection prevention, CRUD operations

Create a class `EmployeeDao` with these methods:

```java
Employee findById(int id) throws SQLException
List<Employee> findByDepartment(int departmentId) throws SQLException
int insert(Employee employee) throws SQLException  // returns new id
boolean update(Employee employee) throws SQLException
boolean delete(int id) throws SQLException
```

Requirements:
- Use `PreparedStatement` for all queries.
- Use `try-with-resources` for all resources.
- `findById` returns `null` if the employee does not exist (or `Optional<Employee>` for a bonus challenge).
- `insert` uses `Statement.RETURN_GENERATED_KEYS` to return the auto-generated id.

---

## Exercise 6 — JDBC: Transactions

**Concept:** Transactions, `setAutoCommit(false)`, `commit()`, `rollback()`

Write a method:
```java
public void transferEmployee(int employeeId, int newDepartmentId) throws SQLException
```

The transfer must also log a record in a `department_history` table:
```sql
CREATE TABLE department_history (
    id              SERIAL PRIMARY KEY,
    employee_id     INT NOT NULL,
    old_department  INT,
    new_department  INT NOT NULL,
    changed_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Both the employee update and the history insert must succeed together, or both must be rolled back. If the new department does not exist, the method must throw an exception and roll back.

---

## Exercise 7 — JDBC: Connection Pool

**Concept:** HikariCP, DataSource

Refactor your `EmployeeDao` from Exercise 5 to use HikariCP instead of `DriverManager.getConnection(...)`.

Create a singleton `DataSourceProvider`:
```java
public class DataSourceProvider {
    public static DataSource getDataSource() { ... }
}
```

Configure the pool with:
- Max pool size: 10
- Minimum idle: 2
- Connection timeout: 30 seconds

Verify that the pool is reused (connections are not created each time) by checking HikariCP logs.

---

## Exercise 8 — Spring Boot: Entity and Repository

**Concept:** @Entity, JpaRepository, Spring Data query methods

Create a Spring Boot project with:
- A `Product` entity: id, name, category, price, quantity, createdAt
- A `ProductRepository` extending `JpaRepository<Product, Long>`
- Custom finder methods:
  - `findByCategory(String category)`
  - `findByPriceBetween(double min, double max)`
  - `findByQuantityLessThan(int threshold)` — for low stock
  - `findByCategoryOrderByPriceAsc(String category)`

Write a simple `CommandLineRunner` (a Spring Boot feature for running code on startup) to insert 5 test products and verify the finders work.

---

## Exercise 9 — Spring Boot: REST API

**Concept:** @RestController, @GetMapping, @PostMapping, ResponseEntity, @Valid

Build a REST API for the `Product` entity from Exercise 8.

Endpoints required:
| Method | URL | Description |
|--------|-----|-------------|
| `GET` | `/api/products` | List all products |
| `GET` | `/api/products/{id}` | Get one product |
| `GET` | `/api/products?category=Electronics` | Filter by category |
| `POST` | `/api/products` | Create a product |
| `PUT` | `/api/products/{id}` | Update a product |
| `DELETE` | `/api/products/{id}` | Delete a product |

Requirements:
- Use separate `CreateProductRequest` and `ProductResponse` records (do not expose the entity directly).
- Validate all input with `@Valid` and appropriate annotations (`@NotBlank`, `@Positive`, etc.).
- Return correct status codes (200, 201, 204, 404).
- Handle `EntityNotFoundException` with a `@ControllerAdvice` handler returning 404.

---

## Exercise 10 — Spring Boot: Full Backend Feature

**Concept:** Layered architecture, service layer, transactions, error handling

Extend your Product API from Exercise 9 with stock management:

1. Add a `POST /api/products/{id}/restock` endpoint that accepts `{"quantity": 50}` and increases the product's quantity.
2. Add a `POST /api/products/{id}/sell` endpoint that accepts `{"quantity": 5}`, decreases stock, and throws a custom `InsufficientStockException` if there is not enough stock.
3. The `InsufficientStockException` should return a 409 Conflict response from the global exception handler.
4. Both stock operations should be `@Transactional`.
5. Write a `StockService` class (following §12.1 SRP — the service has one responsibility: stock management) with methods `restock(Long productId, int quantity)` and `sell(Long productId, int quantity)`.
6. The controller should call `StockService`; the `StockService` should call `ProductRepository`.

Apply Amalitech §12.5 DIP: `StockService` should depend on `ProductRepository` through its interface (injected via constructor), not instantiated directly.

---

## Checklist

Before moving to Phase 14, confirm you can:

- [ ] Write SQL for creating tables with keys and constraints
- [ ] Write SELECT, INSERT, UPDATE, DELETE with WHERE clauses
- [ ] Use JOIN to combine data from two tables
- [ ] Use GROUP BY and aggregate functions to summarize data
- [ ] Connect to PostgreSQL using JDBC
- [ ] Execute queries using PreparedStatement (not Statement) for user input
- [ ] Close JDBC resources with try-with-resources
- [ ] Implement transactions with commit and rollback
- [ ] Configure and use a HikariCP connection pool
- [ ] Create a Spring Boot application with @Entity and JpaRepository
- [ ] Build a REST API with correct HTTP methods and status codes
- [ ] Validate input with @Valid and return 400 for invalid requests
- [ ] Handle exceptions globally with @ControllerAdvice
