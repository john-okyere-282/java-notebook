# Java Learning Notebook — From Zero to Production-Ready Java Developer

![Java Version](https://img.shields.io/badge/Java-21%2B-orange?style=flat-square&logo=java)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)
![Maintained](https://img.shields.io/badge/Maintained-Yes-success?style=flat-square)

---

## Introduction

This repository is a structured, self-paced learning notebook for anyone who wants to go from writing their first line of Java to building production-grade systems with confidence. It is designed for developers who prefer learning by doing — every phase combines clear explanations, working code examples, exercises, and real-world context. Whether you are an absolute beginner picking up Java as your first language, a developer switching from another language like Python or JavaScript, or an experienced programmer who wants to deepen their understanding of the Java ecosystem, this notebook has a path for you.

The notebook is organized into 17 phases (numbered 00 through 16), each building on the last. You will not just memorize syntax — you will understand the *why* behind every concept, learn idiomatic Java patterns, and develop the habits of a professional software engineer. By the end, you will be equipped to build REST APIs, work with databases, write clean and testable code, and contribute to real Java projects at Amalitech and beyond.

---

## Table of Contents

| Phase | Title | Level |
|-------|-------|-------|
| [00 — Environment Setup](./00-environment-setup/README.md) | Setting up JDK 21, IntelliJ IDEA, Maven, and Git | Beginner |
| [01 — Java Fundamentals](./01-java-fundamentals/README.md) | Syntax, variables, data types, operators, control flow | Beginner |
| [02 — Object-Oriented Programming Basics](./02-oop-basics/README.md) | Classes, objects, constructors, methods, fields | Beginner |
| [03 — OOP Advanced Concepts](./03-oop-advanced/README.md) | Inheritance, polymorphism, encapsulation, abstraction | Beginner–Intermediate |
| [04 — Java Core APIs](./04-core-apis/README.md) | String, Math, Arrays, StringBuilder, Date/Time | Intermediate |
| [05 — Collections Framework](./05-collections/README.md) | List, Set, Map, Queue, iterators, generics | Intermediate |
| [06 — Exception Handling](./06-exceptions/README.md) | try-catch-finally, custom exceptions, best practices | Intermediate |
| [07 — File I/O and Serialization](./07-file-io/README.md) | Reading/writing files, streams, NIO, serialization | Intermediate |
| [08 — Functional Programming in Java](./08-functional/README.md) | Lambdas, streams, method references, Optional | Advanced |
| [09 — Concurrency and Multithreading](./09-concurrency/README.md) | Threads, executors, synchronization, CompletableFuture | Advanced |
| [10 — Java Memory Model and JVM Internals](./10-jvm-internals/README.md) | Heap, stack, GC, class loading, JIT compiler | Advanced |
| [11 — Design Patterns](./11-design-patterns/README.md) | Creational, structural, behavioral patterns with Java examples | Advanced |
| [12 — Clean Code and SOLID Principles](./12-clean-code/README.md) | Naming, refactoring, SOLID, DRY, KISS, YAGNI | Advanced |
| [13 — Build Tools and Project Structure](./13-build-tools/README.md) | Maven, Gradle, project layout, dependency management | Professional |
| [14 — Testing in Java](./14-testing/README.md) | JUnit 5, Mockito, TDD, integration testing | Professional |
| [15 — Spring Boot and REST APIs](./15-spring-boot/README.md) | Spring Boot, REST, JPA, security, configuration | Professional |
| [16 — Databases and Persistence](./16-databases/README.md) | JDBC, JPA, Hibernate, SQL, transactions | Professional |

---

## Learning Roadmap

For the full visual roadmap with phase-by-phase breakdowns, weekly study plans, and progression tips, see **[LEARNING-ROADMAP.md](./LEARNING-ROADMAP.md)**.

### Quick Overview

```
BEGINNER (4–6 weeks)       INTERMEDIATE (6–8 weeks)
Phases 00 → 03             Phases 04 → 07
         ↓                          ↓
ADVANCED (8–10 weeks)      PROFESSIONAL (6–8 weeks)
Phases 08 → 12             Phases 13 → 16
```

| Path | Phases | Estimated Time | Outcome |
|------|--------|----------------|---------|
| Beginner | 00–03 | 4–6 weeks | Write working Java programs; understand OOP |
| Intermediate | 04–07 | 6–8 weeks | Use the Java standard library confidently |
| Advanced | 08–12 | 8–10 weeks | Write efficient, clean, production-quality code |
| Professional | 13–16 | 6–8 weeks | Build and test real applications with Spring Boot |

---

## How to Use This Notebook

### 1. Work sequentially through the phases
Each phase assumes knowledge from previous ones. Even if a topic looks familiar, skim it — you may find gaps or Java-specific nuances you haven't seen before.

### 2. Read the README in each phase folder first
Every phase folder contains its own `README.md` that explains goals, key concepts, and what you should be able to do by the end. Read it before diving into code examples.

### 3. Run every code example yourself
Do not just read the examples — type them out, run them, and modify them. Break things intentionally. The best way to understand a concept is to see what happens when it fails.

### 4. Complete the exercises
Each phase ends with exercises ranging from simple recall questions to small projects. Attempt each one before looking at the solutions.

### 5. Update your PROGRESS.md
Use [PROGRESS.md](./PROGRESS.md) to track what you've completed. Checking off items gives you a clear picture of where you are and what comes next.

### 6. Use the GLOSSARY
When you encounter an unfamiliar term, check [GLOSSARY.md](./GLOSSARY.md) first. It is written for learners, not encyclopedias.

### 7. Review coding standards
Keep the [Amalitech Coding Standards](https://github.com/john-okyere-282/amalitech-coding-standards) open in another tab. As you write code, refer to it to ensure your style aligns with professional expectations from the start.

---

## Prerequisites

You do not need prior Java experience. However, you will get more out of this notebook if you:

- Understand basic programming concepts (variables, loops, conditions) in any language
- Are comfortable using the command line for basic operations (navigating directories, running commands)
- Know how to use Git at a beginner level (clone, commit, push)
- Have a working computer with internet access for installing tools

If you are brand new to programming altogether, consider spending one week on a programming fundamentals course first, then return here.

---

## Tools & Setup

The following tools are required. All are free. Installation instructions are covered in depth in [Phase 00 — Environment Setup](./00-environment-setup/README.md).

| Tool | Version | Purpose | Download |
|------|---------|---------|----------|
| **JDK 21** | 21 (LTS) | Java compiler and runtime | [adoptium.net](https://adoptium.net) |
| **IntelliJ IDEA** | 2024.x Community or Ultimate | Primary IDE | [jetbrains.com/idea](https://www.jetbrains.com/idea/) |
| **Apache Maven** | 3.9+ | Build tool and dependency management | [maven.apache.org](https://maven.apache.org) |
| **Git** | 2.40+ | Version control | [git-scm.com](https://git-scm.com) |

### Verifying Your Setup

After installation, run these commands in your terminal to confirm everything is working:

```bash
java --version
# Expected: java 21.x.x ...

javac --version
# Expected: javac 21.x.x

mvn --version
# Expected: Apache Maven 3.x.x ...

git --version
# Expected: git version 2.x.x
```

---

## Companion Repository

This notebook is paired with the **Amalitech Coding Standards** repository, which defines the style guides, naming conventions, code review expectations, and architectural patterns used in professional Java work at Amalitech.

🔗 **[https://github.com/john-okyere-282/amalitech-coding-standards](https://github.com/john-okyere-282/amalitech-coding-standards)**

As you progress through the intermediate and advanced phases, you will be asked to apply those standards directly in exercises. Cross-referencing both repositories is intentional — getting comfortable with standards early removes friction when you join a team project.

---

## Repository Structure

```
java-notebook/
├── README.md                   ← You are here
├── LEARNING-ROADMAP.md         ← Detailed study plan and phase breakdown
├── PROGRESS.md                 ← Personal progress tracker with checklists
├── GLOSSARY.md                 ← Definitions for 80+ Java and programming terms
├── .gitignore                  ← Ignores build outputs, IDE files, secrets
│
├── 00-environment-setup/
│   ├── README.md
│   └── exercises/
├── 01-java-fundamentals/
│   ├── README.md
│   ├── examples/
│   └── exercises/
├── 02-oop-basics/
│   ├── README.md
│   ├── examples/
│   └── exercises/
├── ...
└── 16-databases/
    ├── README.md
    ├── examples/
    └── exercises/
```

---

## Contributing

This notebook is a living document. If you find an error, a better explanation, a missing concept, or want to contribute an exercise or code example:

1. Fork the repository
2. Create a branch: `git checkout -b fix/phase-01-typo` or `feature/add-exercise-03`
3. Make your changes following the [Amalitech Coding Standards](https://github.com/john-okyere-282/amalitech-coding-standards)
4. Write a clear commit message: `fix: correct variable shadowing explanation in phase 03`
5. Open a pull request with a description of what changed and why
6. Wait for review — feedback will be constructive and specific

All contributions, from fixing a single typo to adding a full phase exercise set, are welcome.

---

## License

This project is licensed under the MIT License. You are free to use, copy, modify, and distribute this material for personal or educational purposes. See `LICENSE` for details.

---

*Built for learners who want to understand Java deeply, not just pass a certification.*
