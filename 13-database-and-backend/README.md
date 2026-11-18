# Phase 13 — Database and Backend Development

**Path:** 🔴 Professional  
**Estimated duration:** 2–3 weeks

---

## What This Phase Covers

This phase takes you from pure Java programming into the world of backend software development: storing data in relational databases, connecting Java to those databases via JDBC, building applications with Spring Boot, and exposing functionality over HTTP through REST APIs.

By the end of this phase you will be able to build a complete backend service: a Spring Boot application that accepts HTTP requests, validates input, processes business logic, persists data in PostgreSQL, and returns well-structured JSON responses.

---

## Prerequisites

Before starting this phase, you should be comfortable with:

- Java fundamentals (variables, control flow, arrays)
- Object-Oriented Programming (classes, interfaces, inheritance, encapsulation)
- Exception handling (`try-catch`, custom exceptions, `try-with-resources`)
- Collections (`ArrayList`, `HashMap`)
- Maven project structure (`pom.xml`, dependencies, build lifecycle)
- Functional programming basics (lambdas, streams)

If any of these feel uncertain, revisit Phases 01–12 before continuing.

---

## Topics in This Phase

| Folder | Topic | Difficulty |
|--------|-------|------------|
| `sql-basics/` | Relational databases and SQL | 🟢 Beginner |
| `jdbc/` | Java Database Connectivity | 🔵 Intermediate |
| `spring-boot/` | Spring Boot and Spring Data JPA | 🟠 Advanced |
| `rest-api/` | Building REST APIs with Spring Boot | 🟠 Advanced |
| `exercises/` | Practice exercises | Mixed |

---

## Recommended Study Order

```
sql-basics → jdbc → spring-boot → rest-api → exercises
```

You should understand SQL before writing JDBC code, and understand JDBC before moving to Spring Data JPA (which is an abstraction on top of JDBC).

---

## Tools You Will Need

| Tool | Purpose | Install |
|------|---------|---------|
| PostgreSQL 16+ | Relational database | postgresql.org |
| pgAdmin or DBeaver | Database GUI client | Optional but helpful |
| IntelliJ IDEA | IDE with Spring Boot support | jetbrains.com |
| Apache Maven | Build tool | Already installed |
| Postman or curl | Testing HTTP endpoints | postman.com |

---

## Learning Outcomes

After completing this phase you will be able to:

- Write SQL queries to create, read, update, and delete data
- Design a simple relational schema with foreign keys and normalization
- Connect Java to PostgreSQL using JDBC and execute queries safely with `PreparedStatement`
- Manage database connections with a connection pool (HikariCP)
- Build a Spring Boot application from scratch
- Use Spring Data JPA to persist entities without writing SQL by hand
- Build a REST API with full CRUD operations
- Validate incoming request data and return meaningful error responses
- Apply dependency injection (referencing Amalitech §12.5 DIP)

---

## Connection to the Amalitech Coding Standards

This phase is where the SOLID principles from §12 become immediately practical:

- **§12.5 DIP** — Spring Boot's `@Autowired` and constructor injection are dependency inversion in action. Your controllers depend on service interfaces, not concrete classes.
- **§12.1 SRP** — Separate your code into Controller → Service → Repository layers. Each class has one job.
- **§4.1 / §4.2** — Keep service methods small and single-purpose.
- **§9 TDD** — Write tests for your services and controllers.

---

## What Comes Next

After this phase:

- **Phase 14 — Software Engineering Practices:** Ties together clean code, SOLID, design patterns, and Git workflows.
- **Phase 15 — Projects:** Apply everything you have learned by building real applications, including a full Spring Boot REST API project.
- **Phase 16 — Interview Preparation:** Backend and database knowledge is heavily tested in Java technical interviews.
