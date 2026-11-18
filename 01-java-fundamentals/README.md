# Phase 01 — Java Fundamentals

This phase covers the core building blocks of every Java program: how to store data, what types that data can have, how to manipulate it, how to get input from a user, and how to write clean, well-commented code.

**Prerequisite:** Phase 00 complete — you have JDK 21 installed, IntelliJ IDEA set up, and can run HelloWorld.

---

## Topics in This Phase

| Sub-topic | What you will learn |
|-----------|-------------------|
| [Variables](./variables/README.md) | Declaring and initialising variables; naming rules |
| [Data Types](./data-types/README.md) | The 8 primitive types; reference types; type casting |
| [Operators](./operators/README.md) | Arithmetic, comparison, logical, assignment, ternary |
| [Strings](./strings/README.md) | String immutability, the string pool, common methods, StringBuilder |
| [User Input](./user-input/README.md) | Reading keyboard input with Scanner |
| [Comments](./comments/README.md) | Single-line, multi-line, Javadoc; Amalitech comment standards |
| [Debugging](./debugging/README.md) | Error types, reading stack traces, IntelliJ debugger |
| [Exercises](./exercises/README.md) | 15 exercises covering all topics above |

---

## Learning Goals

By the end of this phase you should be able to:

- [ ] Declare variables of any primitive type and assign values to them
- [ ] Explain the difference between primitives and reference types
- [ ] Use all arithmetic, comparison, logical, and assignment operators correctly
- [ ] Avoid integer division surprises by casting to `double` when needed
- [ ] Work with Strings: create, compare, and use at least ten String methods
- [ ] Read user input from the keyboard using `Scanner`
- [ ] Write clear, appropriate comments following Amalitech §10 standards
- [ ] Read a Java stack trace and identify the error type and line number
- [ ] Complete all 15 exercises without looking at solutions

---

## How to Work Through This Phase

1. **Read** the README for each sub-topic from top to bottom.
2. **Type out** every code example by hand (do not copy-paste). Muscle memory matters.
3. **Run** each example in IntelliJ. Modify it and observe what changes.
4. **Answer** the questions you will find in some examples (they are marked with 💭).
5. **Complete** the exercises at the end of the phase before moving to Phase 02.

---

## Key Tools You Will Use

```java
// Printing output
System.out.println("text");     // prints with newline
System.out.print("text");       // prints without newline
System.out.printf("%.2f", 3.14); // formatted output

// Reading input
import java.util.Scanner;
Scanner scanner = new Scanner(System.in);
String name = scanner.nextLine();
int age = scanner.nextInt();
scanner.close();
```

---

## Amalitech Standards Referenced in This Phase

| Standard | Rule |
|----------|------|
| §3.1 | Use intention-revealing names |
| §3.5 | No Hungarian notation; no `_` or `$` prefix |
| §10.1 | Comment the *why*, not the *what* |
| §10.2 | No journal / change-log comments in code |
| §10.3 | No positional markers (`// ---- section ----`) |
| §11.1 | One variable declaration per line |

---

## Quick Reference — Primitive Types

| Type | Size | Example |
|------|------|---------|
| `byte` | 8 bit | `byte b = 100;` |
| `short` | 16 bit | `short s = 32000;` |
| `int` | 32 bit | `int i = 42;` |
| `long` | 64 bit | `long l = 10_000_000_000L;` |
| `float` | 32 bit | `float f = 3.14f;` |
| `double` | 64 bit | `double d = 3.14159;` |
| `char` | 16 bit | `char c = 'A';` |
| `boolean` | 1 bit | `boolean b = true;` |
