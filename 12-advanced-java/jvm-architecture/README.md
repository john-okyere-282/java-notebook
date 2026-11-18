# JVM Architecture

## What Is the JVM?

The Java Virtual Machine (JVM) is the runtime that executes Java bytecode. When you run `java MyApp`, the JVM:
1. Loads `.class` files (bytecode).
2. Verifies bytecode for safety.
3. Interprets or compiles it to native machine code.
4. Manages memory (allocates for new objects, frees unused ones via GC).

The JVM is what makes Java "write once, run anywhere" — the same bytecode runs on any OS with a JVM.

---

## JVM Memory Areas

```
┌─────────────────────────────────────────────────────────────────┐
│                            JVM                                  │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                         HEAP                            │   │
│  │   ┌─────────────────────────────────────────────────┐   │   │
│  │   │              Young Generation                   │   │   │
│  │   │   ┌────────────┐  ┌──────────┐  ┌──────────┐   │   │   │
│  │   │   │  Eden Space│  │Survivor 0│  │Survivor 1│   │   │   │
│  │   │   └────────────┘  └──────────┘  └──────────┘   │   │   │
│  │   └─────────────────────────────────────────────────┘   │   │
│  │   ┌─────────────────────────────────────────────────┐   │   │
│  │   │              Old Generation (Tenured)            │   │   │
│  │   └─────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌──────────────────┐  ┌────────────────────────────────────┐  │
│  │  Metaspace        │  │  JVM Stack (one per thread)        │  │
│  │  (class metadata) │  │  ┌──────────────────────────────┐ │  │
│  └──────────────────┘  │  │  Stack Frame (per method call) │ │  │
│                         │  │  - local variables             │ │  │
│  ┌──────────────────┐  │  │  - operand stack               │ │  │
│  │  PC Register      │  │  │  - method reference            │ │  │
│  │  (per thread)     │  │  └──────────────────────────────┘ │  │
│  └──────────────────┘  └────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Heap

The heap is where **all objects** (and arrays) are allocated. It is shared across all threads. The JVM's garbage collector manages the heap.

The heap is divided into generations, based on the observation that most objects are short-lived ("infant mortality hypothesis"):

**Young Generation:**
- **Eden Space** — new objects are allocated here. When Eden fills up, a Minor GC runs.
- **Survivor 0 and Survivor 1** — objects that survive a Minor GC are moved here. They alternate roles each GC cycle.
- Objects that survive many GC cycles are **promoted** to the Old Generation.

**Old Generation (Tenured):**
- Long-lived objects live here. When the Old Generation fills, a Major (Full) GC runs. Full GCs are expensive.

### JVM Stack

Each thread has its own stack. It stores **stack frames** — one per method call. A frame holds:
- **Local variables** (including method parameters)
- **Operand stack** (working area for calculations)
- **Return address**

The stack is not garbage collected — frames are pushed on method call and popped on return. `StackOverflowError` occurs when the stack depth exceeds its limit (usually deep or infinite recursion).

### Metaspace

Stores class metadata: bytecode, field names, method signatures, and constant pool data. In Java 8+, Metaspace replaced the older "PermGen" (Permanent Generation). Unlike PermGen, Metaspace is allocated in native memory and can grow dynamically. `OutOfMemoryError: Metaspace` usually means a class-loading leak (e.g. dynamically generated classes not being cleaned up).

### PC Register (Program Counter)

Each thread has a PC register that holds the address of the currently executing JVM instruction. It is an internal JVM detail — you do not interact with it directly.

---

## Garbage Collection

The GC automatically reclaims memory from objects that are no longer reachable from any live thread's stack or static references.

### Mark-Sweep-Compact

The classic algorithm:
1. **Mark** — starting from GC roots (stack references, static fields), traverse all reachable objects and mark them live.
2. **Sweep** — scan the heap and reclaim all unmarked (unreachable) objects.
3. **Compact** — move live objects together to eliminate fragmentation (not all collectors do this).

### Generational Garbage Collection

Minor GC collects only the Young Generation. It is fast (seconds to milliseconds) because most short-lived objects die young. Major/Full GC collects the whole heap, including Old Generation — this pauses the application ("stop-the-world" pause).

---

## GC Algorithms

| Algorithm | Java version | Description | Best for |
|-----------|-------------|-------------|---------|
| **G1GC** | Java 9+ default | Divides heap into regions; aims for predictable pause times | General purpose; large heaps |
| **ZGC** | Java 15+ production | Low-latency; most work concurrent; pauses < 1ms | Latency-sensitive applications |
| **Serial GC** | All versions | Single-threaded; simpler | Small applications, low memory |
| **Parallel GC** | Pre-Java 9 default | Multi-threaded for throughput | Batch processing |
| **Shenandoah** | GraalVM / OpenJDK | Concurrent compaction; very low pauses | Similar to ZGC |

```bash
# Select GC at startup
java -XX:+UseG1GC        MyApp
java -XX:+UseZGC         MyApp
java -XX:+UseSerialGC    MyApp
```

---

## Class Loading

Classes are loaded on first use, not at startup. The class loading system follows a delegation model.

### ClassLoader Hierarchy

```
Bootstrap ClassLoader (built into JVM, written in C++)
    └── loads java.lang.*, java.util.*, etc. from rt.jar / core modules
        │
        ▼
Platform ClassLoader (Java 9+, formerly Extension ClassLoader)
    └── loads Java SE platform extension classes
        │
        ▼
Application (System) ClassLoader
    └── loads classes from your application's classpath
        │
        ▼
Custom ClassLoaders (optional — frameworks like Spring use these)
```

### Delegation Model

When a classloader is asked to load a class:
1. It delegates to its **parent** first.
2. If the parent cannot load it, it tries itself.

This ensures core Java classes (`java.lang.String`) are always loaded by the Bootstrap classloader, preventing malicious code from replacing them.

### Three Phases of Class Loading

1. **Loading** — reads the `.class` file and creates a `Class` object in memory.
2. **Linking** — verifies bytecode, prepares static fields (sets defaults), optionally resolves symbolic references.
3. **Initialisation** — runs the class's `static` initialiser blocks and assigns static field values.

---

## JIT Compiler

The JVM starts by **interpreting** bytecode. As it identifies frequently executed ("hot") code paths, the **JIT (Just-In-Time) compiler** compiles those paths to native machine code for the host CPU. Subsequent calls run the native code — no interpretation overhead.

JIT optimisations include:
- **Inlining** — replacing a method call with the method's body.
- **Escape analysis** — if an object never leaves a method, allocate it on the stack instead of the heap (no GC overhead).
- **Loop unrolling** — rewrite a loop body multiple times to reduce loop overhead.

This is why Java performance improves after a "warm-up" period.

---

## Useful JVM Flags

```bash
# Set initial and maximum heap size
java -Xms512m -Xmx2g MyApp

# Print GC activity
java -Xlog:gc MyApp

# Enable heap dump on OutOfMemoryError
java -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/heapdump.hprof MyApp

# Print JIT compilation activity
java -XX:+PrintCompilation MyApp
```
