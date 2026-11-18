# Phase 15 — Projects

This phase is where learning becomes doing. You have absorbed the concepts. Now you build real applications — without being walked through every line.

---

## Project Philosophy

Each project is a specification, not a tutorial. You will read what the application must do, design the solution yourself, and implement it. This is what professional development looks like. The person who writes your ticket does not write your code.

**Attempt every project independently before looking at any reference solution.**

When you are stuck, re-read the relevant phase notes. When you have finished, compare your implementation to the reference solution — not to copy it, but to understand different approaches to the same problem.

---

## Learning Progression

The projects are ordered by complexity. Each tier adds new requirements on top of the previous.

```
🟢 BEGINNER (01–05)
   ↓ Core Java: OOP, collections, control flow, basic error handling
   ↓ Command-line programs, no external dependencies

🔵 INTERMEDIATE (06–10)
   ↓ File I/O, CSV persistence
   ↓ More complex OOP relationships
   ↓ Stream API for filtering and aggregation

🟠 ADVANCED (11–15)
   ↓ JDBC or Spring Boot
   ↓ PostgreSQL database
   ↓ REST APIs, security, full test suites
```

---

## Project List

### 🟢 Beginner Projects

| # | Project | Key Skills |
|---|---------|-----------|
| 01 | Command-Line Calculator | Methods, control flow, error handling, loops |
| 02 | Student Grade Management System | OOP, ArrayList, averages |
| 03 | Banking Application | Encapsulation, custom exceptions, transfers |
| 04 | Number Guessing Game | Random, loops, scoring |
| 05 | Employee Management System | OOP, ArrayList, HashMap, sorting |

### 🔵 Intermediate Projects

| # | Project | Key Skills |
|---|---------|-----------|
| 06 | Library Management System | OOP relationships, file I/O, CSV, custom exceptions |
| 07 | Inventory Management System | HashMap, file I/O, streams, filtering |
| 08 | Expense Tracker | LocalDate, streams, groupingBy, CSV export |
| 09 | Contact Management System | Collections, search, file persistence |
| 10 | File-Based Student Management | CSV persistence, full CRUD |

### 🟠 Advanced Projects

| # | Project | Key Skills |
|---|---------|-----------|
| 11 | JDBC Employee Management System | JDBC, PostgreSQL, HikariCP, repository pattern |
| 12 | PostgreSQL Inventory System | JDBC, SQL, transactions |
| 13 | Spring Boot REST API (Task Manager) | Spring Boot, JPA, REST, validation, testing |
| 14 | Authentication API | Spring Security, JWT, BCrypt |
| 15 | Complete Employee Backend | Full-stack backend, relationships, pagination |

---

## How to Approach Each Project

1. **Read the full specification** before writing any code. Understand all requirements.
2. **Design first.** On paper or in a text file, sketch: What classes do you need? What are the relationships? What does the data look like?
3. **Build incrementally.** Start with the data model, then the core operations, then the user interface.
4. **Test as you build.** Do not write 500 lines then run the program for the first time.
5. **Apply the standards.** Every project is an opportunity to apply Amalitech §3 naming, §4 functions, §12 SOLID.
6. **Compare to the solution** only after you have a working implementation.

---

## Project Folder Structure

```
15-projects/
├── README.md                     ← this file
├── beginner/
│   ├── 01-calculator/
│   ├── 02-student-grade-system/
│   ├── 03-banking-application/
│   ├── 04-number-guessing-game/
│   └── 05-employee-management/
├── intermediate/
│   ├── 06-library-management/
│   ├── 07-inventory-management/
│   ├── 08-expense-tracker/
│   ├── 09-contact-management/
│   └── 10-file-student-management/
├── advanced/
│   ├── 11-jdbc-employee-management/
│   ├── 12-postgresql-inventory/
│   ├── 13-spring-boot-rest-api/
│   ├── 14-auth-api/
│   └── 15-employee-backend/
└── solutions/
    └── README.md
```

---

## Amalitech Standards to Apply

Every project is a chance to practice:

- **§3** — Descriptive names; no abbreviations; `UPPER_SNAKE_CASE` for constants
- **§4** — Small methods; one responsibility per method; no flag arguments
- **§7** — Consistent formatting; braces always; blank lines between concepts
- **§10** — No commented-out code; no journal comments; comment the why
- **§12** — SOLID principles in every design decision
- **§9** — Write tests for your business logic (optional for beginner, expected for intermediate and advanced)
