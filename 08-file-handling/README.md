# Phase 08 — File Handling and I/O

## Overview

Java provides two main APIs for working with files: the original `java.io` package and the modern `java.nio.file` package introduced in Java 7 (NIO.2). Understanding both is important because you will encounter both in production codebases, but new code should prefer NIO.2.

## Why File I/O Matters

Almost every real application reads configuration files, writes logs, processes CSVs, or stores user data. Knowing how to do this correctly — handling encoding, closing resources, and catching errors — is a core backend skill.

## Topics in This Phase

| Topic | File | Description |
|-------|------|-------------|
| File class (legacy) | [file-class/README.md](file-class/README.md) | The `java.io.File` class for metadata and directory operations |
| Reading and Writing | [reading-writing/README.md](reading-writing/README.md) | FileReader, FileWriter, BufferedReader, BufferedWriter |
| NIO.2 (modern) | [nio-paths/README.md](nio-paths/README.md) | `Path`, `Paths`, `Files` utility class — the preferred API |
| Exercises | [exercises/README.md](exercises/README.md) | 10 exercises to practice file handling |

## Key Principles

- **Always close resources.** Use try-with-resources so streams are closed even on exceptions.
- **Specify the charset.** Default platform encoding varies by OS. Always use `StandardCharsets.UTF_8` explicitly.
- **Prefer NIO.2** for new code — it has a cleaner API, better error messages, and more capabilities.
- **Never swallow IOExceptions.** Log them or re-throw them; silently ignoring them hides real problems.

## Prerequisites

- Phase 05 (Exception Handling) — file operations throw checked exceptions
- Phase 03 (OOP) — streams are objects with lifecycles
