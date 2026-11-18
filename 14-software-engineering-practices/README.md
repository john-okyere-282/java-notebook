# Phase 14 — Software Engineering Practices

**Path:** 🔴 Professional  
**Estimated duration:** 2–3 weeks

---

## What This Phase Covers

This phase ties together the professional craft of software engineering. Writing code that works is necessary but not sufficient. Professional engineers write code that is readable, maintainable, testable, and consistent with the team's standards.

This phase covers four interconnected areas:

1. **Clean Code** — naming, functions, structure, comments, and formatting as defined in the Amalitech Coding Standards
2. **SOLID Principles** — the five design principles that lead to flexible, maintainable software (§12 of the Amalitech Coding Standards, with the official Java examples)
3. **Design Patterns** — reusable solutions to recurring design problems
4. **Git Practices** — version control, branching, commit conventions, and code review

---

## Why This Phase Matters

You can build applications without understanding these principles. Many developers do. The difference shows when:

- The codebase grows to 100,000 lines and no one can find anything
- A requirement changes and updating it requires editing 20 files
- A new developer joins and cannot understand what the code does
- A bug is introduced and no one knows which change caused it

The practices in this phase are what separate a developer who can build features from an engineer who can build maintainable systems.

---

## Connection to the Amalitech Coding Standards

This entire phase is built on the Amalitech Coding Standards (Java Edition). The clean code and SOLID materials reproduce the official rules and examples directly.

| Topic | Standard Sections |
|-------|-----------------|
| Clean Code | §2 General principles, §3 Naming, §4 Functions, §7 Formatting, §8 Objects and Data Structures, §10 Comments |
| SOLID Principles | §12.1 SRP, §12.2 OCP, §12.3 LSP, §12.4 ISP, §12.5 DIP |
| Git Practices | §13 Team Responsibility (code review context) |

---

## Topics in This Phase

| Folder | Topic |
|--------|-------|
| `clean-code/` | Naming, functions, formatting, code smells, refactoring |
| `solid-principles/` | All five SOLID principles with official Amalitech examples |
| `design-patterns/` | Singleton, Factory, Builder, Strategy, Observer, Decorator, Adapter |
| `git-practices/` | Branching, commits, PRs, code review |
| `exercises/` | Refactoring and design exercises |

---

## Recommended Study Order

```
clean-code → solid-principles → design-patterns → git-practices → exercises
```

Clean code and SOLID are foundational to the design patterns discussion. Git practices can be read at any point but should be applied from day one.

---

## Learning Outcomes

After completing this phase you will be able to:

- Identify and fix code smells: long methods, large classes, feature envy, primitive obsession
- Apply all five SOLID principles with Java examples
- Recognize and implement common design patterns
- Write meaningful Git commit messages following Conventional Commits
- Conduct and respond to code reviews professionally
- Refactor existing code to improve readability without changing behavior
