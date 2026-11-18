# SQL Basics

SQL (Structured Query Language) is the language used to communicate with relational databases. Every backend developer needs it. This document covers everything you need to get started, using a simple employee database as the running example.

---

## 1. Relational Database Concepts

### What Is a Relational Database?

A relational database stores data in **tables** (also called relations). Each table is like a spreadsheet with named columns and typed rows. The power comes from connecting tables together through keys.

### Tables, Rows, and Columns

| Term | Meaning | Example |
|------|---------|---------|
| **Table** | A named collection of related data | `employees` |
| **Column** | A named attribute with a type | `first_name VARCHAR(100)` |
| **Row** | One record (one instance of data) | A single employee |
| **Cell** | The value at a specific row/column intersection | `'Alice'` |

### Primary Keys

A **primary key** uniquely identifies each row in a table. No two rows can share the same primary key value.

```sql
CREATE TABLE employees (
    id          SERIAL PRIMARY KEY,   -- auto-incrementing integer
    first_name  VARCHAR(100) NOT NULL,
    last_name   VARCHAR(100) NOT NULL,
    email       VARCHAR(255) UNIQUE NOT NULL,
    department  VARCHAR(100),
    salary      NUMERIC(10, 2),
    hire_date   DATE NOT NULL
);
```

Here `id` is the primary key. `SERIAL` means PostgreSQL automatically assigns an incrementing integer.

### Foreign Keys

A **foreign key** in one table references the primary key in another table. This creates a relationship between tables and enforces referential integrity — you cannot reference a row that does not exist.

```sql
CREATE TABLE departments (
    id    SERIAL PRIMARY KEY,
    name  VARCHAR(100) NOT NULL
);

CREATE TABLE employees (
    id            SERIAL PRIMARY KEY,
    first_name    VARCHAR(100) NOT NULL,
    last_name     VARCHAR(100) NOT NULL,
    email         VARCHAR(255) UNIQUE NOT NULL,
    department_id INT REFERENCES departments(id),  -- foreign key
    salary        NUMERIC(10, 2),
    hire_date     DATE NOT NULL
);
```

Now each employee's `department_id` points to a row in the `departments` table. If you try to insert an employee with a `department_id` that doesn't exist, the database will reject it.

### Common Data Types in PostgreSQL

| Type | Use for |
|------|---------|
| `INTEGER` / `INT` | Whole numbers |
| `SERIAL` | Auto-incrementing integer (shorthand) |
| `BIGINT` | Large whole numbers |
| `NUMERIC(p, s)` | Exact decimal numbers (money, measurements) |
| `REAL` / `DOUBLE PRECISION` | Floating-point numbers |
| `VARCHAR(n)` | Variable-length string, max n characters |
| `TEXT` | Unlimited-length string |
| `BOOLEAN` | true / false |
| `DATE` | Calendar date (year, month, day) |
| `TIMESTAMP` | Date and time |
| `TIMESTAMPTZ` | Date and time with timezone |

---

## 2. SQL Basics: SELECT, INSERT, UPDATE, DELETE

These four statements are called **DML** (Data Manipulation Language). They are what you will use most.

### SELECT — Read Data

```sql
-- Select all columns from a table
SELECT * FROM employees;

-- Select specific columns
SELECT first_name, last_name, salary FROM employees;

-- Give columns an alias
SELECT first_name AS "First Name", salary AS "Monthly Salary"
FROM employees;

-- Select with a computed column
SELECT first_name, last_name, salary * 12 AS annual_salary
FROM employees;
```

### INSERT — Add Data

```sql
-- Insert one row (specify columns explicitly — always do this)
INSERT INTO employees (first_name, last_name, email, department_id, salary, hire_date)
VALUES ('Alice', 'Johnson', 'alice@company.com', 1, 75000.00, '2022-03-15');

-- Insert multiple rows at once
INSERT INTO employees (first_name, last_name, email, department_id, salary, hire_date)
VALUES
    ('Bob',   'Smith',  'bob@company.com',  2, 65000.00, '2021-07-01'),
    ('Carol', 'Davis',  'carol@company.com', 1, 80000.00, '2020-01-10'),
    ('David', 'Wilson', 'david@company.com', 3, 70000.00, '2023-05-20');
```

### UPDATE — Modify Data

```sql
-- Update a specific row (always use WHERE — without it, every row is updated)
UPDATE employees
SET salary = 85000.00
WHERE id = 1;

-- Update multiple columns
UPDATE employees
SET salary = 90000.00, department_id = 2
WHERE email = 'alice@company.com';

-- Update based on a condition
UPDATE employees
SET salary = salary * 1.10   -- 10% raise
WHERE department_id = 1;
```

> ⚠️ **Always include a `WHERE` clause in `UPDATE` and `DELETE`.** Without it, every row in the table is affected.

### DELETE — Remove Data

```sql
-- Delete a specific row
DELETE FROM employees WHERE id = 3;

-- Delete all employees in a department (careful!)
DELETE FROM employees WHERE department_id = 2;

-- Delete all rows from a table (dangerous — do not run carelessly)
DELETE FROM employees;
```

---

## 3. The WHERE Clause

`WHERE` filters which rows a statement affects. It supports a wide range of conditions.

```sql
-- Exact match
SELECT * FROM employees WHERE department_id = 1;

-- Comparison operators: =, <>, !=, <, >, <=, >=
SELECT * FROM employees WHERE salary > 70000;

-- Range
SELECT * FROM employees WHERE salary BETWEEN 60000 AND 80000;

-- List of values
SELECT * FROM employees WHERE department_id IN (1, 3);

-- Pattern matching (% = any characters, _ = one character)
SELECT * FROM employees WHERE last_name LIKE 'S%';        -- starts with S
SELECT * FROM employees WHERE email LIKE '%@company.com'; -- ends with domain

-- NULL checks
SELECT * FROM employees WHERE department_id IS NULL;
SELECT * FROM employees WHERE department_id IS NOT NULL;

-- Combining conditions
SELECT * FROM employees
WHERE salary > 70000
  AND department_id = 1;

SELECT * FROM employees
WHERE department_id = 1
   OR department_id = 2;

-- NOT
SELECT * FROM employees WHERE NOT department_id = 3;
```

---

## 4. JOINs

JOINs combine rows from two or more tables based on a related column.

### INNER JOIN

Returns only rows where there is a match in both tables. Rows with no match are excluded.

```sql
-- Get employee names with their department names
SELECT e.first_name, e.last_name, d.name AS department
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;
```

Result: Only employees who have a department (employees where `department_id IS NULL` are excluded).

### LEFT JOIN (LEFT OUTER JOIN)

Returns all rows from the left table, plus matching rows from the right table. If there is no match, the right table columns contain `NULL`.

```sql
-- Get all employees, even those with no department assigned
SELECT e.first_name, e.last_name, d.name AS department
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;
```

### RIGHT JOIN (RIGHT OUTER JOIN)

Returns all rows from the right table, plus matching rows from the left. The mirror image of LEFT JOIN.

```sql
-- Get all departments, including empty ones
SELECT d.name AS department, e.first_name, e.last_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;
```

### Joining More Than Two Tables

```sql
SELECT
    e.first_name,
    e.last_name,
    d.name AS department,
    p.title AS project
FROM employees e
INNER JOIN departments d ON e.department_id = d.id
INNER JOIN project_assignments pa ON e.id = pa.employee_id
INNER JOIN projects p ON pa.project_id = p.id;
```

---

## 5. ORDER BY and GROUP BY

### ORDER BY — Sort Results

```sql
-- Sort by salary descending (highest first)
SELECT first_name, last_name, salary
FROM employees
ORDER BY salary DESC;

-- Sort by department, then by name within each department
SELECT first_name, last_name, department_id
FROM employees
ORDER BY department_id ASC, last_name ASC;
```

`ASC` (ascending, default) and `DESC` (descending) control the sort direction.

### GROUP BY — Aggregate Data

`GROUP BY` collapses multiple rows into summary rows. It is used with aggregate functions.

| Function | Returns |
|----------|---------|
| `COUNT(*)` | Number of rows |
| `COUNT(column)` | Number of non-NULL values |
| `SUM(column)` | Total |
| `AVG(column)` | Average |
| `MIN(column)` | Smallest value |
| `MAX(column)` | Largest value |

```sql
-- Count employees per department
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;

-- Average salary per department, with department names
SELECT d.name AS department, AVG(e.salary) AS avg_salary
FROM employees e
INNER JOIN departments d ON e.department_id = d.id
GROUP BY d.name
ORDER BY avg_salary DESC;

-- Departments with more than 3 employees (HAVING filters after grouping)
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 3;
```

> `WHERE` filters rows before grouping. `HAVING` filters groups after grouping.

---

## 6. Database Design Basics — Normalization

Normalization is the process of organizing a database to reduce data redundancy and improve integrity.

### First Normal Form (1NF)
- Each column contains atomic (indivisible) values.
- Each row is unique.

**Bad (violates 1NF):** Storing multiple phone numbers in one column: `"555-1234, 555-5678"`.  
**Good:** One phone number per row in a separate `phone_numbers` table.

### Second Normal Form (2NF)
- Satisfies 1NF.
- Every non-key column depends on the entire primary key (no partial dependencies).

This matters most when you have a composite primary key.

### Third Normal Form (3NF)
- Satisfies 2NF.
- No transitive dependencies: non-key columns depend only on the primary key, not on other non-key columns.

**Bad:** Storing `department_name` directly in the `employees` table. If the department name changes, you update it in every employee row.  
**Good:** Separate `departments` table. `employees` stores `department_id`. Change the name in one place.

### Practical Rule
If you find yourself repeating the same string data across many rows, that data probably belongs in its own table with a foreign key reference.

---

## 7. Complete Example: Employee Database

```sql
-- Create the schema
CREATE TABLE departments (
    id    SERIAL PRIMARY KEY,
    name  VARCHAR(100) NOT NULL
);

CREATE TABLE employees (
    id            SERIAL PRIMARY KEY,
    first_name    VARCHAR(100) NOT NULL,
    last_name     VARCHAR(100) NOT NULL,
    email         VARCHAR(255) UNIQUE NOT NULL,
    department_id INT REFERENCES departments(id) ON DELETE SET NULL,
    salary        NUMERIC(10, 2) NOT NULL DEFAULT 0,
    hire_date     DATE NOT NULL
);

-- Seed departments
INSERT INTO departments (name) VALUES ('Engineering'), ('Marketing'), ('Finance');

-- Seed employees
INSERT INTO employees (first_name, last_name, email, department_id, salary, hire_date) VALUES
    ('Alice',  'Johnson', 'alice@company.com',  1, 95000, '2020-03-15'),
    ('Bob',    'Smith',   'bob@company.com',    1, 88000, '2021-07-01'),
    ('Carol',  'Davis',   'carol@company.com',  2, 72000, '2019-11-20'),
    ('David',  'Wilson',  'david@company.com',  3, 81000, '2022-01-10'),
    ('Eve',    'Martinez','eve@company.com',    2, 69000, '2023-05-15');

-- Query: list all employees with their departments
SELECT e.first_name, e.last_name, d.name AS department, e.salary
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id
ORDER BY d.name, e.last_name;

-- Query: average salary by department
SELECT d.name, ROUND(AVG(e.salary), 2) AS avg_salary, COUNT(*) AS headcount
FROM employees e
INNER JOIN departments d ON e.department_id = d.id
GROUP BY d.name
ORDER BY avg_salary DESC;

-- Query: employees earning above average
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees)
ORDER BY salary DESC;
```

---

## Key Takeaways

- Tables store data in rows and columns. Primary keys identify rows uniquely. Foreign keys link tables.
- `SELECT`, `INSERT`, `UPDATE`, `DELETE` are the four core DML statements.
- Always use `WHERE` with `UPDATE` and `DELETE` unless you intentionally want to affect all rows.
- `JOIN` combines tables. `INNER JOIN` requires a match; `LEFT JOIN` keeps all rows from the left table.
- `GROUP BY` with aggregate functions (`COUNT`, `SUM`, `AVG`) produces summary data.
- Normalization reduces duplication by moving repeated data into its own table.
