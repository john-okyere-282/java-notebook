# Phase 09 — Packages and Build Tools

## Overview

As Java projects grow, source files must be organised and the build process must be automated. This phase covers two related topics: Java's package system for organising code, and build tools (Maven and Gradle) for managing dependencies and automating compilation, testing, and deployment.

## Why This Matters

A project with 200+ classes in a single directory is unmaintainable. A project with no build tool forces every developer to manually download JAR files, configure classpaths, and run compiler commands. Packages and build tools solve both problems.

## Topics in This Phase

| Topic | File | Description |
|-------|------|-------------|
| Packages | [packages/README.md](packages/README.md) | Declarations, imports, naming, access rules |
| Maven | [maven/README.md](maven/README.md) | The standard Java build tool — POM, lifecycle, dependencies |
| Gradle | [gradle/README.md](gradle/README.md) | Modern alternative to Maven |
| Exercises | [exercises/README.md](exercises/README.md) | 5 exercises |

## AmaliTech Standards Reference

- **§3.5** — Package naming convention: `com.amalitech.<projectname>`
- **§11.1** — No wildcard imports (`import java.util.*` is forbidden)

## Prerequisites

- Phase 01–07 (Java fundamentals)
- Basic command-line familiarity
