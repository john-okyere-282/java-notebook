# Phase 10 — Testing

## Why Testing Matters

Untested code is a liability. Without automated tests:
- You cannot change code with confidence — any change might break something you won't notice until it reaches production.
- Debugging takes longer because failures appear far from their cause.
- New team members cannot safely refactor.

Tests are not overhead — they are the evidence that your code works. They also act as living documentation: a well-written test describes exactly what a method should do, in plain executable terms.

## AmaliTech Standards (§9)

AmaliTech's coding standards mandate:
- **100% meaningful coverage** — every code path should have a test.
- **Single concept per test** — each test method verifies exactly one behaviour.
- **Clean and readable tests** — test code is production code; it must be maintained.
- **Meaningful assertions** — `assertTrue(result != null)` tells you nothing; `assertNotNull(user, "User should be created")` tells you what failed and why.

## Topics in This Phase

| Topic | File | Description |
|-------|------|-------------|
| JUnit 5 | [junit5/README.md](junit5/README.md) | The standard Java testing framework |
| Mockito | [mockito/README.md](mockito/README.md) | Mocking dependencies in unit tests |
| TDD | [tdd/README.md](tdd/README.md) | Test-Driven Development — write tests first |
| Exercises | [exercises/README.md](exercises/README.md) | 15 hands-on testing exercises |

## The Testing Pyramid

```
        /\
       /  \
      / E2E \          ← few, slow, expensive
     /--------\
    / Integration\     ← some, moderately fast
   /--------------\
  /   Unit Tests   \   ← many, fast, cheap
 /------------------\
```

The majority of your tests should be unit tests (fast, isolated, no I/O). Integration tests verify that components work together. End-to-end tests verify full user flows.

## Prerequisites

- Phase 09 (Maven) — tests are run via `mvn test`
- Phase 03–07 (OOP, collections, exceptions)
