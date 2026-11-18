# Phase 12 — Advanced Java

## Overview

This phase covers topics that separate intermediate Java developers from senior ones: understanding how the JVM works, writing correct concurrent code, using the reflection API, and reasoning about performance.

## Topics in This Phase

| Topic | File | Description |
|-------|------|-------------|
| JVM Architecture | [jvm-architecture/README.md](jvm-architecture/README.md) | Memory areas, garbage collection, class loading, JIT |
| Concurrency | [concurrency/README.md](concurrency/README.md) | Threads, synchronization, ExecutorService, CompletableFuture |
| Reflection | [reflection/README.md](reflection/README.md) | Inspecting and invoking code at runtime |
| Performance | [performance/README.md](performance/README.md) | Profiling, common mistakes, optimisation guidance |
| Exercises | [exercises/README.md](exercises/README.md) | 10 exercises |

## Why These Topics Matter

- **JVM knowledge** helps you understand `OutOfMemoryError`, GC pauses, and class loading issues that appear in production.
- **Concurrency** is essential for any application handling more than one request at a time — which is every server.
- **Reflection** underpins frameworks (Spring, Hibernate, JUnit) that you use every day. Understanding it demystifies "magic" annotations.
- **Performance** reasoning prevents the most common classes of Java performance bugs before they reach production.

## Prerequisites

- Phases 01–11 (all prior phases)
- Familiarity with exceptions, generics, and collections
