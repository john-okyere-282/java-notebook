# JDBC — Java Database Connectivity

JDBC is the standard Java API for connecting to relational databases. It is a low-level API, meaning you write SQL yourself and manually map result sets to Java objects. Understanding JDBC is essential before moving to higher-level frameworks like Spring Data JPA, because those frameworks are built on top of it.

---

## 1. What Is JDBC?

JDBC (Java Database Connectivity) is a Java API defined in `java.sql` and `javax.sql`. It provides a standard interface for:

- Opening a connection to a database
- Sending SQL statements
- Reading results
- Managing transactions

Different databases (PostgreSQL, MySQL, SQLite) each provide a **JDBC driver** — a jar file that implements the JDBC interfaces for that database. Your code targets the standard JDBC interfaces; the driver handles the database-specific communication.

```
Your Java code
      ↓
JDBC API (java.sql.*)
      ↓
JDBC Driver (e.g. org.postgresql.Driver)
      ↓
PostgreSQL Database
```

---

## 2. JDBC Driver Setup

Add the PostgreSQL driver to your Maven `pom.xml`:

```xml
<dependencies>
    <!-- PostgreSQL JDBC Driver -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.7.3</version>
    </dependency>

    <!-- HikariCP connection pool (covered later) -->
    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

---

## 3. The Core JDBC Classes

| Class / Interface | Location | Purpose |
|-------------------|----------|---------|
| `DriverManager` | `java.sql` | Opens raw connections |
| `Connection` | `java.sql` | Represents an open database connection |
| `Statement` | `java.sql` | Executes a static SQL string |
| `PreparedStatement` | `java.sql` | Executes a parameterized SQL query |
| `ResultSet` | `java.sql` | Holds the rows returned by a SELECT query |
| `SQLException` | `java.sql` | Checked exception for database errors |
| `DataSource` | `javax.sql` | Factory for connections (used by connection pools) |

---

## 4. Opening a Connection

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseConnection {

    private static final String URL      = "jdbc:postgresql://localhost:5432/company_db";
    private static final String USERNAME = "postgres";
    private static final String PASSWORD = "your_password";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USERNAME, PASSWORD);
    }
}
```

The URL format for PostgreSQL: `jdbc:postgresql://host:port/database_name`

> In production, never hardcode credentials. Load them from environment variables or a secrets manager.

---

## 5. Statement vs PreparedStatement

### Statement — Static SQL (Avoid for User Input)

`Statement` executes a fixed SQL string. It is fine for SQL with no user-supplied values.

```java
try (Connection conn = DatabaseConnection.getConnection();
     Statement stmt = conn.createStatement()) {

    ResultSet rs = stmt.executeQuery("SELECT id, first_name, salary FROM employees");
    while (rs.next()) {
        System.out.println(rs.getInt("id") + " " + rs.getString("first_name"));
    }
}
```

### PreparedStatement — Parameterized SQL (Always Use for User Input)

`PreparedStatement` uses `?` placeholders for parameters. The driver sends the SQL and parameters separately, so user input can never be interpreted as SQL.

```java
String sql = "SELECT * FROM employees WHERE department_id = ? AND salary > ?";

try (Connection conn = DatabaseConnection.getConnection();
     PreparedStatement ps = conn.prepareStatement(sql)) {

    ps.setInt(1, departmentId);    // first ?
    ps.setDouble(2, minimumSalary); // second ?

    try (ResultSet rs = ps.executeQuery()) {
        while (rs.next()) {
            System.out.println(rs.getString("first_name") + " " + rs.getDouble("salary"));
        }
    }
}
```

### SQL Injection — Why PreparedStatement Matters

Imagine a login form. If you build SQL by concatenation:

```java
// DANGEROUS — never do this
String sql = "SELECT * FROM users WHERE email = '" + email + "' AND password = '" + password + "'";
```

An attacker can enter `' OR '1'='1` as the password, turning the query into:

```sql
SELECT * FROM users WHERE email = 'alice@test.com' AND password = '' OR '1'='1'
```

This always returns a row, bypassing authentication. `PreparedStatement` prevents this because the input is never interpreted as SQL — it is treated as a literal string value.

---

## 6. Reading a ResultSet

```java
String sql = "SELECT id, first_name, last_name, salary, hire_date FROM employees";

try (Connection conn = DatabaseConnection.getConnection();
     PreparedStatement ps = conn.prepareStatement(sql);
     ResultSet rs = ps.executeQuery()) {

    while (rs.next()) {
        int id           = rs.getInt("id");
        String firstName = rs.getString("first_name");
        String lastName  = rs.getString("last_name");
        double salary    = rs.getDouble("salary");
        Date hireDate    = rs.getDate("hire_date");

        System.out.printf("%d  %-15s %-15s  %.2f  %s%n",
            id, firstName, lastName, salary, hireDate);
    }
}
```

`rs.next()` advances the cursor one row and returns `false` when there are no more rows.

ResultSet getter methods: `getInt`, `getString`, `getDouble`, `getBoolean`, `getDate`, `getTimestamp`, `getBigDecimal`. You can pass either the column name (more readable) or the column index (1-based).

---

## 7. Complete CRUD with JDBC

This example uses an `Employee` record and shows all four operations.

```java
// Employee data carrier
public record Employee(int id, String firstName, String lastName,
                       String email, int departmentId, double salary) {}
```

### CREATE (INSERT)

```java
public int createEmployee(Employee employee) throws SQLException {
    String sql = """
            INSERT INTO employees (first_name, last_name, email, department_id, salary, hire_date)
            VALUES (?, ?, ?, ?, ?, CURRENT_DATE)
            """;

    try (Connection conn = getConnection();
         PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {

        ps.setString(1, employee.firstName());
        ps.setString(2, employee.lastName());
        ps.setString(3, employee.email());
        ps.setInt(4, employee.departmentId());
        ps.setDouble(5, employee.salary());

        ps.executeUpdate();

        try (ResultSet generatedKeys = ps.getGeneratedKeys()) {
            if (generatedKeys.next()) {
                return generatedKeys.getInt(1); // the new id
            }
        }
    }
    throw new SQLException("Creating employee failed, no ID returned.");
}
```

### READ (SELECT)

```java
public Optional<Employee> findById(int id) throws SQLException {
    String sql = "SELECT * FROM employees WHERE id = ?";

    try (Connection conn = getConnection();
         PreparedStatement ps = conn.prepareStatement(sql)) {

        ps.setInt(1, id);

        try (ResultSet rs = ps.executeQuery()) {
            if (rs.next()) {
                return Optional.of(mapRow(rs));
            }
        }
    }
    return Optional.empty();
}

public List<Employee> findAll() throws SQLException {
    String sql = "SELECT * FROM employees ORDER BY last_name, first_name";
    List<Employee> employees = new ArrayList<>();

    try (Connection conn = getConnection();
         PreparedStatement ps = conn.prepareStatement(sql);
         ResultSet rs = ps.executeQuery()) {

        while (rs.next()) {
            employees.add(mapRow(rs));
        }
    }
    return employees;
}

private Employee mapRow(ResultSet rs) throws SQLException {
    return new Employee(
        rs.getInt("id"),
        rs.getString("first_name"),
        rs.getString("last_name"),
        rs.getString("email"),
        rs.getInt("department_id"),
        rs.getDouble("salary")
    );
}
```

### UPDATE

```java
public boolean updateSalary(int employeeId, double newSalary) throws SQLException {
    String sql = "UPDATE employees SET salary = ? WHERE id = ?";

    try (Connection conn = getConnection();
         PreparedStatement ps = conn.prepareStatement(sql)) {

        ps.setDouble(1, newSalary);
        ps.setInt(2, employeeId);

        int rowsAffected = ps.executeUpdate();
        return rowsAffected > 0;
    }
}
```

### DELETE

```java
public boolean deleteEmployee(int id) throws SQLException {
    String sql = "DELETE FROM employees WHERE id = ?";

    try (Connection conn = getConnection();
         PreparedStatement ps = conn.prepareStatement(sql)) {

        ps.setInt(1, id);
        int rowsAffected = ps.executeUpdate();
        return rowsAffected > 0;
    }
}
```

---

## 8. try-with-resources for JDBC

JDBC objects — `Connection`, `Statement`, `PreparedStatement`, `ResultSet` — all implement `AutoCloseable`. Always use `try-with-resources` to close them, or you will leak database connections.

```java
// BAD — connection may never be closed if an exception is thrown
Connection conn = getConnection();
PreparedStatement ps = conn.prepareStatement(sql);
ResultSet rs = ps.executeQuery();
// ... if any exception occurs here, conn is leaked

// GOOD — all resources closed automatically
try (Connection conn = getConnection();
     PreparedStatement ps = conn.prepareStatement(sql);
     ResultSet rs = ps.executeQuery()) {

    // safe to work here
} // conn, ps, rs all closed in reverse order
```

---

## 9. Transactions

A **transaction** groups multiple SQL statements into an atomic unit: either all succeed, or all are rolled back as if none ran. This is essential for operations that must stay consistent — for example, transferring money between accounts (debit one, credit another).

By default JDBC operates in **auto-commit** mode: every statement is immediately committed. For multi-statement transactions, disable auto-commit.

```java
public void transferSalary(int fromId, int toId, double amount) throws SQLException {
    String debit  = "UPDATE employees SET salary = salary - ? WHERE id = ?";
    String credit = "UPDATE employees SET salary = salary + ? WHERE id = ?";

    try (Connection conn = getConnection()) {
        conn.setAutoCommit(false); // start transaction manually

        try (PreparedStatement debitStmt  = conn.prepareStatement(debit);
             PreparedStatement creditStmt = conn.prepareStatement(credit)) {

            debitStmt.setDouble(1, amount);
            debitStmt.setInt(2, fromId);
            debitStmt.executeUpdate();

            creditStmt.setDouble(1, amount);
            creditStmt.setInt(2, toId);
            creditStmt.executeUpdate();

            conn.commit(); // both succeeded — make it permanent

        } catch (SQLException e) {
            conn.rollback(); // either failed — undo everything
            throw new RuntimeException("Transfer failed, transaction rolled back", e);
        }
    }
}
```

**Commit** makes the changes permanent and visible to other connections.  
**Rollback** undoes all changes made since `setAutoCommit(false)`.

---

## 10. Connection Pooling with HikariCP

Opening a database connection is expensive: it involves a TCP handshake, authentication, and session setup. Creating a new connection per request is too slow for a real application.

A **connection pool** maintains a pool of open connections that are reused across requests. HikariCP is the fastest and most widely used pool for Java.

```xml
<!-- pom.xml -->
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>5.1.0</version>
</dependency>
```

```java
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import javax.sql.DataSource;

public class DataSourceFactory {

    private static final HikariDataSource dataSource;

    static {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:postgresql://localhost:5432/company_db");
        config.setUsername("postgres");
        config.setPassword("your_password");
        config.setMaximumPoolSize(10);         // max connections in pool
        config.setMinimumIdle(2);              // keep 2 connections ready
        config.setConnectionTimeout(30_000);   // wait up to 30s for a connection
        config.setIdleTimeout(600_000);        // close idle connections after 10 min
        config.setMaxLifetime(1_800_000);      // replace connections after 30 min

        dataSource = new HikariDataSource(config);
    }

    public static DataSource get() {
        return dataSource;
    }
}
```

Now instead of `DriverManager.getConnection(...)`, use:

```java
try (Connection conn = DataSourceFactory.get().getConnection()) {
    // conn is borrowed from the pool, returned on close
}
```

The connection is returned to the pool (not actually closed) when the try-with-resources block exits.

---

## Key Takeaways

- JDBC provides a standard Java API for database access. The driver is database-specific; your code targets the standard interfaces.
- Use `PreparedStatement` for every query that includes user input. It prevents SQL injection.
- Always close `Connection`, `PreparedStatement`, and `ResultSet` with `try-with-resources`.
- Transactions group multiple statements into one atomic operation. Use `setAutoCommit(false)`, `commit()`, and `rollback()`.
- Use a connection pool (HikariCP) in any real application. Never open a new connection per request.
